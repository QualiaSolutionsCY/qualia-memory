---
title: "Qualia Portal Employee Workflows"
aliases: [employee-workflows, moayad-automation, portal-clock-in, erp-employee-management]
tags: [qualia-erp, employee-management, automation, cron, portal]
sources:
  - "daily/2026-04-26.md"
  - "daily/2026-04-27.md"
created: 2026-04-26
updated: 2026-04-27
---

# Qualia Portal Employee Workflows

The Qualia ERP portal (portal.qualiasolutions.net) includes employee workflow management features covering automated task scheduling, clock-in tracking, manual task assignment, and UI affordances for team management. On 2026-04-26, several improvements were shipped: Moayad's automated morning cron tasks, removal of the generic "Other" clock-in option, a framework update popup, a Delete Client button, and project logo circles on the homepage. On 2026-04-27, the clock-in modal was further refined: activity checkboxes were removed entirely, and Moayad received a hardcoded "Daily Research / Blog" entry pinned at the top of his project list.

## Key Points

- Moayad's morning cron tasks (research + blog post) fire Mon–Fri at 5 AM UTC automatically, with idempotent deduplication to prevent double-runs
- "Other" removed as a clock-in option — employees must now clock in against a specific project, enforcing work attribution (commit `f1014dd`)
- Framework update popup (`qualia-framework@latest install`) gated to admin+employee roles only, dismissed via date-stamped localStorage key (`qualia-framework-update-2026-04-26`)
- `deleteClientRecord` server action existed but had zero UI callers — the Delete Client button on `/clients/[id]` was the missing affordance
- `/clients` (full CRUD) and Control → Clients tab (read-only summary) share the same `clients` table but serve different user intents
- On 2026-04-27, activity checkboxes (Daily Blog / Daily Research / Project Work) were removed from the clock-in modal — modal reverted to project-only selection with Moayad getting a hardcoded "Daily Research / Blog" entry pinned at the top of his project list

## Details

The employee automation system uses cron jobs to handle repetitive daily tasks. Moayad's workflow was configured with two automated tasks (research and blog post writing) that fire every weekday at 5 AM UTC. The idempotent deduplication guard ensures that if the cron fires twice or the process restarts, the tasks are not duplicated. This same pattern of cron + idempotent dedup is used elsewhere in the Qualia ecosystem — notably in the [[concepts/memory-loop-architecture|memory loop's]] weekly knowledge flush.

Two manual tasks were also created for Moayad targeting the Qualia Solutions project: Finish Milestone 1 (09:30–12:30) and Finish Milestone 2 (13:30–17:00). A notable detail is that Milestone 2's phases were not yet defined in `project_phases`, so the task description instructed Moayad to plan M2 phases first before executing — a pragmatic workaround for incomplete planning state.

The framework update popup demonstrates a reusable notification pattern: a date-stamped localStorage key (`qualia-framework-update-YYYY-MM-DD`) controls dismissal. Bumping the date suffix in the key re-triggers the popup after future releases, without needing a database-backed notification system. The popup is role-gated to admin and employee users only — clients and external users never see it.

The orphaned server action discovery is a recurring ERP pattern: when users report missing features, check whether the backend action already exists but lacks a UI caller. In this case, `deleteClientRecord` was fully implemented but unreachable because no button invoked it. The fix was a confirmation dialog on the client detail page, not new backend code.

Three options were proposed for Moayad's remaining morning routine time: LinkedIn outreach automation, CRM hygiene (stale leads, missing follow-ups), and inbox triage. Options 1 + 2 were recommended, with a "First 3 hours" clock-in preset button awaiting Fawzi's final selection.

On 2026-04-27, the clock-in modal was further refined (commit `3dc85fe`). Activity checkboxes (Daily Blog, Daily Research, Project Work, etc.) were removed entirely from the modal for all employees, returning it to project-only selection. For Moayad specifically (matched by `moayad@qualiasolutions.net`), a single hardcoded "Daily Research / Blog" entry was pinned at the top of his project list, styled like a project but with a document icon and primary accent border. When tapped, the work session opens against the Qualia Solutions project (resolved by name match). If Qualia Solutions isn't in his assignments, an inline error instructs him to contact admin. No other employees see this entry. This approach avoids the complexity of a generic activity system while solving Moayad's specific daily workflow need.

A clock-out bug was also discovered on 2026-04-27: Moayad couldn't clock out despite having generated a report, because the report's project name (`"Qualia Solutions — qualiasolutions.net Rebuild"`) didn't match the session's project name (`"Qualia Solutions"`). See [[concepts/erp-clock-out-name-mismatch]] for the full analysis.

## Related Concepts

- [[concepts/memory-loop-architecture]] — Uses the same cron + idempotent dedup pattern for knowledge management
- [[concepts/qualia-erp-phase-25]] — Performance optimizations shipped in the same portal during the same day
- [[concepts/qualia-framework-v4.3.0-release]] — The framework version whose update popup was added to the portal
- [[concepts/erp-clock-out-name-mismatch]] — Clock-out bug caused by project name string identity mismatch between report and session
- [[concepts/supabase-client-access-patterns]] — Portal access patterns for external clients; this article covers the internal employee perspective

## Sources

- [[daily/2026-04-26.md]] — Session (04:09, second instance): "Confirmed Moayad's morning cron tasks fire Mon–Fri at 5 AM UTC automatically, with idempotent dedup"
- [[daily/2026-04-26.md]] — Session (04:09, second instance): "Removed 'Other' as a clock-in option — users must clock in against a project"
- [[daily/2026-04-26.md]] — Session (04:09, second instance): "`deleteClientRecord` action existed but had zero UI callers — always check for orphaned server actions"
- [[daily/2026-04-26.md]] — Session (04:09, second instance): "Framework update popup uses date-stamped localStorage key — bump date suffix to re-trigger"
- [[daily/2026-04-27.md]] — Session at 08:38: "Activity checkboxes gone from clock-in modal. Moayad gets hardcoded 'Daily Research / Blog' entry pinned at top of his project list"
- [[daily/2026-04-27.md]] — Session at 17:30: "Report-to-session project name mismatch blocked Moayad's clock-out — manually force-closed session"
