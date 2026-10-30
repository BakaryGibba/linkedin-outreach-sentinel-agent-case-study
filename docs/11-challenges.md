[Back to README](../README.md)

# 11 Challenges

## Challenge Summary

The hardest problems in this project were not "make Playwright click a button." They were: proving a send went to the right person, keeping the system honest about what it doesn't know, and operating against two external systems (LinkedIn's DOM and the Google Sheets API) that were never designed to be treated as an automation backend.

## Challenge Matrix

| Challenge | Risk | Mitigation |
| --- | --- | --- |
| Wrong-recipient sends | A single point of failure could message the wrong person | Two independent identity checks (profile title, then open-conversation recipient) before any text is typed |
| LinkedIn DOM/selector fragility | Message button or composer selectors change or vary by account/layout | Multiple selector strategies tried per step (`aria-label`, `href` pattern, text content), each logged with a step name for diagnosis |
| Composer truncation or reformatting | LinkedIn's editor could silently alter the approved text | Typed text is read back and compared to the approved draft twice — once after filling, once immediately before the click |
| Google Sheets API rate limits | Bursty batch operations exceed the 60 reads/minute/user quota | Observed directly in production logs (see below); batch endpoints already bound their per-call limits, and this is tracked as a hardening item in [13 Future Improvements](13-future-improvements.md) |
| Duplicate or concurrent sends | A double click, a retried request, or two tabs could send twice | Idempotency keys, `sendLocks` per `(campaign, contact, phase)`, and a `sending_*` reservation status that must be cleared before another attempt is accepted |
| Fabricated personalization | An AI draft could imply familiarity or facts nobody approved | `validateDraft` blocks fabricated-familiarity phrasing and sensitive-attribute references, and runs on AI output, manual edits, and the final pre-send text alike |
| Partial batch failures | One bad contact in a batch of 25 could stop the whole batch | Every batch item is wrapped independently; one failure is recorded in the results array and the loop continues |
| Login/checkpoint interruptions mid-automation | 2FA or a security checkpoint could appear at any navigation | Detected via URL pattern (`/login`, `/checkpoint`, `/authwall`) and surfaced as an explicit "sign in, then retry" error rather than silently hanging or attempting to bypass it |

## A Real Example: Sheets Quota Under Batch Load

The application's own logs captured this failure directly during a batch send operation:

```
GaxiosError: Quota exceeded for quota metric 'Read requests' and limit
'Read requests per minute per user' of service 'sheets.googleapis.com'
... quota_limit_value: "60", quota_metric: "sheets.googleapis.com/read_requests"
```

This happened because `POST /api/batch/send-linkedin` reads `listActivities()` per candidate to check whether a phase was already sent, on top of the reads already performed by `activeQueue()`. At small batch sizes this is invisible; at a larger batch size against the free-tier Sheets quota, it is not. The system degrades safely here — the affected outreach rows are marked `error` with the quota message preserved in `last_error` rather than left in an ambiguous state — but it is the clearest concrete argument in this codebase for the database migration discussed in [13 Future Improvements](13-future-improvements.md).

## A Real Example: Composer Timeout

Also visible in production logs, a `send-linkedin` call failed with:

```
TimeoutError: locator.waitFor: Timeout 30000ms exceeded.
waiting for locator('div.msg-form__contenteditable[contenteditable="true"], ...').last()
to be visible
```

Because this timeout occurs *before* `sendClicked` is set to `true`, the Express layer's failure handler safely reverted the reservation to `error` instead of leaving the record stuck in `sending_initial` — exactly the scenario the `sendClicked` flag exists to distinguish from a failure that happens after the click, where automatic reversal would risk a duplicate send.
