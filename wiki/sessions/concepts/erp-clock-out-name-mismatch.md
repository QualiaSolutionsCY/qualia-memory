---
title: "ERP Clock-Out Name Mismatch Bug"
aliases: [clock-out-bug, project-name-mismatch, report-session-name-mismatch, ilike-matching]
tags: [qualia-erp, bug, data-integrity, string-matching, time-tracking]
sources:
  - "daily/2026-04-27.md"
created: 2026-04-27
updated: 2026-04-27
---

# ERP Clock-Out Name Mismatch Bug

Moayad was blocked from clocking out of the Qualia ERP time-tracking system despite having submitted a session report. The root cause was a project name string identity mismatch: the report was posted with `"Qualia Solutions — qualiasolutions.net Rebuild"` (QS-REPORT-03) while the work session stored the project as `"Qualia Solutions"`. The detection logic in `app/actions/work-sessions.ts:772` uses `ilike` exact-match on `project_name`, which fails when the report has an em-dash suffix variant of the project name.

## Key Points

- **String identity mismatch:** Report project name (`"Qualia Solutions — qualiasolutions.net Rebuild"`) did not match session project name (`"Qualia Solutions"`) — the em-dash suffix variant caused `ilike` to fail
- **`ilike` is not fuzzy:** PostgreSQL `ilike` is case-insensitive but still requires exact substring match — it does not handle partial matches or name variants
- **Manual intervention required:** Moayad's session (`affbb1d5`) had to be force-closed at 13:23 UTC by manually ending the work session, unblocking him immediately
- **Fix direction:** Switch from exact `ilike` match on `project_name` to either matching on `framework_project_id` (stable identifier) or using substring/fuzzy matching so project name variations don't block clock-out

## Details

The clock-out flow in the Qualia ERP requires detecting whether a session report has been submitted before allowing the employee to end their work session. The detection logic at `app/actions/work-sessions.ts:772` queries for reports matching the current session's `project_name` using PostgreSQL `ilike`. This works correctly when the report is created through the standard flow where the project name is inherited from the session. However, when the report is created through an alternative path (such as the framework's `/qualia-report` skill) that resolves the project name differently — including additional context like the site URL — the names diverge.

The bug represents a broader class of data identity problems: using mutable, human-readable strings (project names) as join keys across system boundaries. The project name can legitimately vary depending on context — the ERP stores the short name, the framework appends the site URL for disambiguation, and users may type yet another variant. The correct solution is to match on a stable identifier like `framework_project_id` or `project_id` (UUID), falling back to `project_name` substring match only when the ID is unavailable.

This is the same class of invisible-data bug as the [[concepts/qualiafinal-public-booking-sync|qualiafinal public booking sync issue]], where data existed in the database but was functionally invisible because a required field for the consuming query was missing or mismatched. In both cases, the system appeared to work (report existed, booking existed) but the downstream consumer couldn't find the data due to an identifier mismatch.

## Related Concepts

- [[concepts/qualiafinal-public-booking-sync]] — Same class of bug: data exists but is invisible to queries due to identifier mismatch (workspace_id there, project_name here)
- [[concepts/qualia-portal-employee-workflows]] — The clock-in/clock-out workflow where this bug manifests
- [[concepts/supabase-client-access-patterns]] — Another example of access/data visibility failures caused by missing or mismatched identifiers

## Sources

- [[daily/2026-04-27.md]] — Session at 17:30: "Report was posted with project name `\"Qualia Solutions — qualiasolutions.net Rebuild\"` (QS-REPORT-03), but the work session's project was stored as just `\"Qualia Solutions\"`"
- [[daily/2026-04-27.md]] — Session at 17:30: "Detection logic in `app/actions/work-sessions.ts:772` uses `ilike` exact-match on `project_name`, which fails when the report has an em-dash suffix variant"
- [[daily/2026-04-27.md]] — Session at 17:30: "Manually clocked Moayad out to unblock him immediately rather than fixing the root cause first"
- [[daily/2026-04-27.md]] — Session at 17:21: "Fawzi reported that user 'Moayad' cannot clock out even though a report was generated"
