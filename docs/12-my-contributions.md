[Back to README](../README.md)

# 12 My Contributions

> [!NOTE]
> Built with the engineering team at Apping Technology Sdn. Bhd. during my AI Engineering internship (2026). This page describes my part of the work.

## Contribution Summary

This was a full-stack build: the domain model and state machine, the Google Sheets repository with optimistic concurrency and audit logging, the AI drafting service and its fact-only validator, the Playwright automation service and its double-verification send flow, the CSV/live-capture import pipeline with cross-campaign suppression, the Express API and batch orchestration, the static dashboard frontend, the sheet setup script, and the automated test suite.

## Contribution Areas

| Area | Contribution |
| --- | --- |
| Domain design | Designed the 18-status outreach state machine and its centrally enforced transition rules |
| Data layer | Built the Google Sheets repository, including optimistic concurrency, idempotency-key deduplication, and batched multi-tab writes |
| AI integration | Designed the OpenAI Responses API drafting flow and the fact-only validator shared by AI output, manual edits, and pre-send checks |
| RPA safety | Designed and implemented the double identity-verification send flow and the composer-level guards in the Playwright service |
| Import pipeline | Built the CSV parser, the identity-fingerprinting deduplication scheme, and the live browser-capture alternative |
| Suppression logic | Designed the cross-campaign suppression rules that prevent duplicate or unwanted outreach across overlapping campaigns |
| API design | Built the full Express route set, including batch drafting/approval/sending with per-item failure isolation |
| Frontend | Built the single-page operator dashboard (queue, import, campaign panels) in vanilla JS against the API |
| Operations | Wrote the sheet setup script, the operator guide, the campaign-writing guide, and the recovery/hardening runbook |
| Testing | Authored the 23-test automated suite spanning domain rules, drafting validation, CSV import, LinkedIn automation guards, config parsing, sheet setup, and end-to-end API behaviors |

## Technical Narrative

The core contribution is the safety architecture, not any single feature in isolation. The state machine, the validator, the identity checks, the locks, and the idempotency keys are five separate mechanisms that each independently make it harder to send the wrong message to the wrong person — and the project's test suite (`test/app.test.js`, `test/linkedinAutomation.test.js`, `test/drafting.test.js`) specifically targets the seams between them: what happens when a campaign is paused mid-flow, what happens when a draft greets the wrong name, what happens when a Playwright send fails before versus after the click.

## Test Coverage Snapshot

```
✔ cross-campaign suppression blocks prior outreach but allows captured-only contacts from paused campaigns
✔ report metrics are scoped to the selected campaign
✔ manual send endpoint refuses unconfirmed sends and records confirmed sends
✔ remove endpoint excludes an unsent contact from its campaign queue
✔ Playwright send transitions only after successful browser automation
✔ Playwright send failure does not mark outreach sent
✔ Playwright send blocks a campaign contact with prior send evidence
✔ batch draft generates drafts for active ready contacts
✔ batch LinkedIn send continues after one profile fails
✔ configuration applies safe defaults and normalizes a private key
✔ runtime validation names missing Google configuration
✔ row mapping follows the canonical sheet header order
✔ state machine permits expected transitions and blocks unsafe ones
✔ template drafts meet configured length and meeting requirements
✔ long campaign context preserves a complete closing question
✔ validation rejects unsupported familiarity and sensitive inference
✔ drafting uses deterministic fallback without an API key
✔ normalizes LinkedIn URLs without tracking data
✔ imports, deduplicates, qualifies, and excludes official export rows
✔ LinkedIn automation blocks a draft greeting the wrong recipient before opening a browser
✔ LinkedIn automation blocks an incomplete message before opening a browser
✔ sheet setup creates canonical tabs, headers, filters, and strict validations
✔ follow-up date skips weekends in Kuala Lumpur

23 tests, 23 pass, 0 fail
```
