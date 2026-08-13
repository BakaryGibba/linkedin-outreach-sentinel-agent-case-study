[Back to README](../README.md)

# 13 Future Improvements

## Roadmap Summary

Future work should focus on durability under higher volume, richer operator visibility, and reducing reliance on a single local browser session and a single Sheets API quota bucket.

## Recommended Improvements

| Area | Improvement | Benefit |
| --- | --- | --- |
| Persistence at scale | Migrate the system of record from Google Sheets to Postgres/Supabase once volume exceeds pilot scale, keeping a Sheets export/sync for operator visibility | Removes the per-minute read/write quota ceiling observed directly in production logs (see [11 Challenges](11-challenges.md)) |
| Sheets read reduction | Cache `listOutreach()`/`listActivities()` per request cycle in batch endpoints instead of re-reading per candidate | Fewer API calls per batch operation, less exposure to rate limiting |
| Retry policy | Add bounded retry/backoff around transient Google API errors (429/5xx) before surfacing them as a hard `error` status | Fewer manual recoveries for what is often a transient condition |
| Observability | Add a small ops view over the Activity Log — error rates, send success rates, time-in-status — beyond the current counts-only `/api/report` | Makes systemic issues visible without reading raw Sheets rows |
| Multi-operator support | Add authentication and per-operator `actor` attribution beyond the single `OPERATOR_NAME` config value | Enables team use with accountable per-person audit trails |
| Credential handling | Move the service-account key and OpenAI key out of local `.env`/JSON files and into a managed secret store for any shared/deployed environment | Reduces local secret exposure risk |
| Selector resilience | Add a lightweight self-check that periodically verifies the LinkedIn selectors used in `linkedinAutomation.js` still resolve, independent of a live send attempt | Surfaces LinkedIn UI changes before they cause a mid-send failure |
| Test fixtures for RPA | Add a recorded/mocked Playwright fixture suite for the send flow's DOM-dependent branches (composer state, InMail subject, length warnings) | Exercises failure branches that are hard to hit reliably against the live site |

## Future Architecture

```mermaid
flowchart LR
    UI[Dashboard] --> API[Express API]
    API --> DB[(Postgres / Supabase)]
    API --> SheetsSync[Sheets Sync\n(operator-facing view only)]
    API --> Draft[Drafting Service]
    API --> RPA[LinkedIn Automation]
    API --> Ops[Observability Dashboard]
    Vault[Secret Manager] --> API
    Vault --> RPA
    DB --> SheetsSync
```

## Screenshot

**Planned:** No dedicated screenshot; this document is forward-looking.
