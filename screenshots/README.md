[Back to README](../README.md)

# Screenshots Plan

All captured screenshots below come from a sanitized, throwaway demo workspace: an in-memory repository seeded with fabricated names, companies, and offers, running the real application code (`createApp` from `src/app.js`) against a local demo server, driven with Playwright. The real Google Sheet, the real `Connections.csv`, and the real persistent LinkedIn browser profile were never opened or referenced. The demo server and its seed script were deleted after capture.

## Capture List

| Status | File | What It Captures | Why It Matters |
| --- | --- | --- | --- |
| Captured | `dashboard-queue.png` | The queue tab after a demo import, showing several fabricated contacts across statuses | Shows the primary operator workspace |
| Captured | `contact-card-draft.png` | An open contact card (`Marcus Webb`, fabricated) with a generated initial and follow-up draft and live character counters | Shows the draft review and editing surface |
| Captured | `import-preview.png` | The import panel with a demo campaign selected and a fabricated `demo-connections.csv` attached, ready to import | Shows the CSV import entry point |
| Captured | `campaign-form.png` | The campaign panel: creation form plus the one demo campaign already created | Shows how campaign rules are authored |
| Captured | `send-confirmation.png` | An approved contact (`Elena Cho`, fabricated) showing the Copy / Send / Mark sent actions | Shows the attended-send action surface before a send is triggered |
| Captured | `activity-log.png` | The same contact with Activity expanded, showing a `draft_generated` → `draft_edited` → `draft_approved` history | Shows the audit trail behind every transition |
| Captured | `health-panel.png` | The dashboard header showing the "Ready" integration-health indicator | Shows the health check at a glance |
| Pending | `capture-visible.png` | The "Capture visible" panel after scanning a live connections page | Requires an actual attended LinkedIn session; deliberately not captured to avoid opening a real account or scraping real connections for a portfolio asset |

## Why No Live-Send or Live-Capture Screenshot

Two features in this system — `POST /api/linkedin/capture-visible` and `POST /api/outreach/:id/send-linkedin` — only do anything meaningful against a real, logged-in LinkedIn session. Exercising them for a screenshot would mean either using a real account (exposing real connection data) or a disposable one (still a real send/scrape against LinkedIn's live site, which is exactly the kind of automation this project treats carefully). The rest of the workspace — including the exact UI state immediately before a send (`send-confirmation.png`) — is fully demonstrated without needing to cross that line.

## Capture Rules

- Use a demo/in-memory workspace with fabricated names, companies, and offers — never the production spreadsheet.
- Never drive the persistent Chrome profile (`.linkedin-browser-profile/`) or a real LinkedIn session for a portfolio capture.
- Prefer screenshots of workspace structure and workflow state over anything that would require real personal data to look authentic.
