[Back to README](../README.md)

# 09 API Overview

## Summary

All routes are defined in `src/app.js` and mounted under `/api`, guarded by a single middleware that returns `503` for the entire API surface if Google Sheets isn't configured. Every mutating endpoint accepts an `Idempotency-Key` header (falling back to a body value or a fresh UUID) and most also require an `expectedVersion` for optimistic concurrency. This is a behavior-level overview; see the source for exact request/response shapes.

## Health

| Endpoint | Behavior |
| --- | --- |
| `GET /api/health` | Reports app/google/spreadsheet/schema/openai status. Returns `503` if Sheets is unreachable or the schema doesn't match `SHEETS`. |

## Campaigns

| Endpoint | Behavior |
| --- | --- |
| `GET /api/campaigns` | Lists all campaigns. |
| `POST /api/campaigns` | Creates a campaign; requires `name` and `offer`. Defaults `tone` if not supplied. |
| `POST /api/campaigns/:id/state` | Transitions `active`/`paused`/`completed`. Pausing also force-completes any in-flight `sent_initial`/`followup_due` rows in that campaign. |

## Import

| Endpoint | Behavior |
| --- | --- |
| `POST /api/imports/preview` | Multipart CSV upload; parses, qualifies, and cross-campaign-suppresses rows; returns a `previewId`, totals, warnings, and a 20-row sample. |
| `POST /api/imports/:previewId/commit` | Re-runs suppression against current Sheets state, then batch-writes accepted rows. Preview is single-use and expires after 30 minutes. |

## LinkedIn Capture

| Endpoint | Behavior |
| --- | --- |
| `POST /api/linkedin/capture-visible` | Opens/reuses the persistent browser, scrolls the visible connections page, excludes anyone already suppressed for the target campaign, returns a preview. `409` if another LinkedIn automation task is already running. |
| `POST /api/linkedin/captures/:previewId/commit` | Commits a selected subset of a capture preview; can optionally trigger draft generation for every newly committed row in the same call (`generateDrafts: true`). |

## Drafting

| Endpoint | Behavior |
| --- | --- |
| `POST /api/outreach/:id/draft` | Generates a draft via the drafting service; only legal from `ready_for_draft` or `draft_ready`. |
| `PUT /api/outreach/:id/draft` | Accepts a manually edited draft; runs it through the same `validateDraft` checks as AI output before saving. |
| `POST /api/outreach/:id/approve` | Re-validates both drafts, then transitions to `approved`. Refuses if either draft fails validation. |
| `POST /api/outreach/:id/reject` | Clears drafts and returns the record to `ready_for_draft`. |

## Context and Removal

| Endpoint | Behavior |
| --- | --- |
| `PUT /api/outreach/:id/context` | Updates missing contact fields (name, company, position, relationship context, URL); recomputes `qualification` and transitions to `ready_for_draft` or back to `needs_context` accordingly. |
| `POST /api/outreach/:id/remove` | Removes a contact from a campaign's queue; refuses if a message was already sent or the send status is uncertain. |

## Sending

| Endpoint | Behavior |
| --- | --- |
| `POST /api/outreach/:id/confirm-send` | Manual-send confirmation path (no browser automation) — records `initial_sent_at`/`followup_sent_at` and schedules the follow-up. Requires `confirmed: true`. |
| `POST /api/outreach/:id/send-linkedin` | Attended Playwright send for one record. Checks version, cross-campaign message history, and an in-process lock before reserving the send and calling the automation service. On a safe pre-click failure, automatically reverts the record to `error`. |

## Batch Operations

| Endpoint | Behavior |
| --- | --- |
| `POST /api/batch/drafts` | Drafts up to `limit` (default 25, max 100) queued contacts for a campaign; each item succeeds/fails independently. |
| `POST /api/batch/approve` | Approves up to `limit` `draft_ready` contacts, re-validating each draft first. |
| `POST /api/batch/send-linkedin` | Sends up to `limit` (default 10, max 50) approved/due contacts, applying the exact same lock/reservation/verification sequence as the single-send endpoint to each item in turn. |

## Outcomes and Recovery

| Endpoint | Behavior |
| --- | --- |
| `POST /api/outreach/:id/outcome` | Records a terminal or semi-terminal outcome. If the outcome is `do_not_contact`, it also flags the Contacts row globally and cancels any other non-terminal outreach rows for that person across every campaign. |
| `POST /api/outreach/:id/recover` | Manually moves an `error` record to a target status after operator review. |

## Reporting

| Endpoint | Behavior |
| --- | --- |
| `GET /api/queue` | Returns the active, actionable queue (optionally filtered by status/campaign), sorted by follow-up due date, with `next_action` computed for due follow-ups. |
| `POST /api/followups/refresh` | Scans `sent_initial` rows in active campaigns and transitions any that are past their due date to `followup_due`. |
| `GET /api/report` | Status counts, optionally scoped to a campaign. |
| `GET /api/export.csv` | Streams a CSV export of core outreach fields. |
| `GET /api/outreach/:id/activity` | Returns the Activity Log entries for one outreach record. |
