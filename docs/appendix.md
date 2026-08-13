[Back to README](../README.md)

# Appendix

## Glossary

| Term | Meaning |
| --- | --- |
| Attended automation | Browser automation run in a visible window, with login/checkpoint prompts handled by a human, as opposed to headless/stealth automation |
| Outreach record | One row in the `Outreach` tab: the state of one contact within one campaign |
| Idempotency key | A caller-supplied string logged to the Activity Log; a repeated key is treated as a no-op duplicate, not a new write |
| Optimistic concurrency | The `version`/`expectedVersion` check that rejects a write based on a stale read instead of locking rows |
| Fact-only validation | The rule set in `validateDraft` that blocks fabricated familiarity, sensitive-attribute references, and mismatched greetings |
| Cross-campaign suppression | The rule set that prevents a contact already in progress, already messaged, or opted out in one campaign from being re-imported into another |
| Terminal status | A status (`meeting_booked`, `not_interested`, `do_not_contact`, `completed`, `excluded`) that removes a record from the active queue permanently |
| Template fallback | The deterministic, non-AI draft writer that is always available regardless of OpenAI API availability |

## Safety Checklist

Do not include in this case study, or in any exported asset:

- `.env` files, the `service-account.json` key, or any OpenAI/Google credential value.
- The real `Connections.csv` export or any real contact name, email, company, or LinkedIn URL.
- `server.stdout.log` / `server.stderr.log` in full — these can contain real profile URLs and Google API responses. Only short, hand-picked, sanitized excerpts (as used in [11 Challenges](11-challenges.md)) belong here, with identifiers such as spreadsheet IDs and project numbers removed.
- The `.linkedin-browser-profile/` persistent browser session data.
- Any real campaign offer, audience criteria, or proof point tied to an actual client or employer.

## Reviewer Checklist

| Reviewer | What To Look For |
| --- | --- |
| Hiring manager | End-to-end ownership: domain modeling, API design, RPA safety engineering, and test discipline in one project |
| Automation engineer | The double-verification send flow, the lock/reservation pattern, and how failures before vs. after the click are distinguished |
| AI product reviewer | The fact-only validator shared across AI and non-AI drafting paths, and the deterministic fallback design |
| Graduate supervisor | Problem framing around trust and account safety, and the trade-off reasoning in [10 Engineering Decisions](10-engineering-decisions.md) |

## Source Project Reference

This case study, titled **Outreach Sentinel**, documents a project internally named **Apping AI RPA: Outreach** (package name `linkedin-outreach-copilot` in its source `package.json`). Source layout referenced throughout: `src/app.js`, `src/domain/model.js`, `src/config.js`, `src/repositories/googleSheetsRepository.js`, `src/services/drafting.js`, `src/services/linkedinAutomation.js`, `src/services/connectionImporter.js`, `src/services/sheetSetup.js`, `src/public/`, and `test/`.
