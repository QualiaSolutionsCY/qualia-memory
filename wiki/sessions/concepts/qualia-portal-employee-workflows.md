---
title: "Qualia Portal Employee Workflows"
aliases: [employee-workflows, moayad-automation, portal-clock-in, erp-employee-management]
tags: [qualia-erp, employee-management, automation, cron, portal]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Qualia Portal Employee Workflows

The Qualia ERP portal (portal.qualiasolutions.net) includes employee workflow management features covering automated task scheduling, clock-in tracking, manual task assignment, and UI affordances for team management. On 2026-04-26, several improvements were shipped: Moayad's automated morning cron tasks, removal of the generic "Other" clock-in option, a framework update popup, a Delete Client button, and project logo circles on the homepage.

## Key Points

- Moayad's morning cron tasks (research + blog post) fire Mon–Fri at 5 AM UTC automatically, with idempotent deduplication to prevent double-runs
- "Other" removed as a clock-in option — employees must now clock in against a specific project, enforcing work attribution (commit `f1014dd`)
- Framework update popup (`qualia-framework@latest install`) gated to admin+employee roles only, dismissed via date-stamped localStorage key (`qualia-framework-update-2026-04-26`)
- `deleteClientRecord` server action existed but had zero UI callers — the Delete Client button on `/clients/[id]` was the missing affordance
- `/clients` (full CRUD) and Control → Clients tab (read-only summary) share the same `clients` table but serve different user intents

## Details

The employee automation system uses cron jobs to handle repetitive daily tasks. Moayad's workflow was configured with two automated tasks (research and blog post writing) that fire every weekday at 5 AM UTC. The idempotent deduplication guard ensures that if the cron fires twice or the process restarts, the tasks are not duplicated. This same pattern of cron + idempotent dedup is used elsewhere in the Qualia ecosystem — notably in the [[concepts/memory-loop-architecture|memory loop's]] weekly knowledge flush.

Two manual tasks were also created for Moayad targeting the Qualia Solutions project: Finish Milestone 1 (09:30–12:30) and Finish Milestone 2 (13:30–17:00). A notable detail is that Milestone 2's phases were not yet defined in `project_phases`, so the task description instructed Moayad to plan M2 phases first before executing — a pragmatic workaround for incomplete planning state.

The framework update popup demonstrates a reusable notification pattern: a date-stamped localStorage key (`qualia-framework-update-YYYY-MM-DD`) controls dismissal. Bumping the date suffix in the key re-triggers the popup after future releases, without needing a database-backed notification system. The popup is role-gated to admin and employee users only — clients and external users never see it.

The orphaned server action discovery is a recurring ERP pattern: when users report missing features, check whether the backend action already exists but lacks a UI caller. In this case, `deleteClientRecord` was fully implemented but unreachable because no button invoked it. The fix was a confirmation dialog on the client detail page, not new backend code.

Three options were proposed for Moayad's remaining morning routine time: LinkedIn outreach automation, CRM hygiene (stale leads, missing follow-ups), and inbox triage. Options 1 + 2 were recommended, with a "First 3 hours" clock-in preset button awaiting Fawzi's final selection.

## Related Concepts

- [[concepts/memory-loop-architecture]] — Uses the same cron + idempotent dedup pattern for knowledge management
- [[concepts/qualia-erp-phase-25]] — Performance optimizations shipped in the same portal during the same day
- [[concepts/qualia-framework-v4.3.0-release]] — The framework version whose update popup was added to the portal

## Sources

- [[daily/2026-04-26.md]] — Session (04:09, second instance): "Confirmed Moayad's morning cron tasks fire Mon–Fri at 5 AM UTC automatically, with idempotent dedup"
- [[daily/2026-04-26.md]] — Session (04:09, second instance): "Removed 'Other' as a clock-in option — users must clock in against a project"
- [[daily/2026-04-26.md]] — Session (04:09, second instance): "`deleteClientRecord` action existed but had zero UI callers — always check for orphaned server actions"
- [[daily/2026-04-26.md]] — Session (04:09, second instance): "Framework update popup uses date-stamped localStorage key — bump date suffix to re-trigger"
