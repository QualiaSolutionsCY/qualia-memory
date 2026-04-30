---
title: "Underdog Academy Quiz Validation Bug"
aliases: [quiz-validation-bug, zod-schema-mismatch, silent-scoring-failure, option-id-validation]
tags: [underdog-sales, zod, validation, bug, silent-failure, quiz]
sources:
  - "daily/2026-04-29.md"
created: 2026-04-29
updated: 2026-04-29
---

# Underdog Academy Quiz Validation Bug

A Zod schema mismatch on Underdog Academy (ai.underdogsales.com) silently broke 100% of all quizzes — 113 MCQ single-answer and 50 MCQ multi-answer questions — not just the initially reported True/False type. The `validateAnswerForQuestion` function used `uuidSchema` to validate option IDs, but option IDs are arbitrary strings by design (`"a"`, `"b"`, `"true"`, `"false"`), not UUIDs. Every answer submission failed Zod validation and scored 0, with no error surfaced to users. Every paying user was locked out of gated lessons behind quizzes.

## Key Points

- **100% quiz breakage, not just True/False:** Initial report said "True/False broken" — a DB-wide audit (`WHERE NOT (id ~ uuid_regex)`) revealed all 163 questions had non-UUID option IDs, meaning every quiz was silently scoring 0
- **Root cause:** `validateAnswerForQuestion` used `z.string().uuid()` (uuidSchema) to validate option IDs — but option IDs are arbitrary strings like `"a"`, `"b"`, `"true"`, `"false"`, not UUIDs. Question-ID keys ARE UUIDs; option-ID values are not
- **Fix:** Changed option-ID validation from `uuidSchema` to `z.string().min(1).max(64)` — preserves input sanitization while matching actual data shape
- **No error surfaced to users:** Zod returned `{ok: false}` for every answer, scoring every quiz 0 — users saw wrong answers but no error message explaining why
- **DB audit before schema assumptions:** Always verify schema constraints match actual data, especially after DB seeding or migration — the seeded data used a different ID convention than the validation expected

## Details

The bug was discovered on 2026-04-29 when True/False quizzes were reported as broken on Underdog Academy. The initial hypothesis was a True/False-specific issue, but a database audit immediately revealed the scope was far larger: every question in the database used non-UUID option IDs. The question table had been seeded with option IDs like `"a"`, `"b"`, `"c"`, `"d"` for MCQ questions and `"true"`, `"false"` for binary questions. Meanwhile, the answer validation function applied `z.string().uuid()` to these values, which rejects anything that isn't a valid UUID v4 string.

The failure mode is particularly insidious because it presents as a scoring error, not a validation error. When Zod validation fails on the option ID, the function returns `{ok: false}` — the same shape as a wrong answer. The UI interprets this as "the user answered incorrectly" rather than "the system failed to validate the answer." Users see their answers marked wrong with no explanation, and quiz completion gates prevent access to subsequent lessons. For a paid educational platform, this means every paying customer was unable to progress through gated content.

The fix was minimal: changing the option-ID field from `uuidSchema` to `z.string().min(1).max(64)`. This preserves basic input sanitization (non-empty, bounded length) while accepting the actual data format. The question-ID keys (which ARE UUIDs) were left with `uuidSchema` validation. This distinction — keys that identify database rows should be UUIDs, values that identify choices within a question are arbitrary strings — was not enforced or documented in the original schema design.

The broader lesson applies to any system where database seeding and runtime validation are developed independently. When a seeding script populates data with one convention (arbitrary string IDs) and the validation layer assumes another (UUID format), the mismatch is invisible until runtime. A data integrity check that validates seeded data against the runtime schema would catch this class of bug immediately after seeding, before any user encounters it.

A secondary fix was also applied: the leaderboard query was updated to filter out users with 0 XP (`.gt("xp_total", 0)` / `.gt("xp_this_week", 0)`) to remove ghost entries, and the quiz feedback UI was fixed to prevent tick/cross icons from overlapping question text on mobile viewports.

## Related Concepts

- [[connections/silent-failure-swallowing]] — Fifth instance of the silent failure pattern: validation returns `{ok: false}` instead of surfacing an error, presenting identically to a wrong answer
- [[concepts/sophia-lead-routing-debug]] — Same class of bug: truthy/falsy return value swallowed the real error. There, `{success: false}` was truthy; here, `{ok: false}` was treated as "wrong answer" instead of "validation failure"
- [[concepts/vercel-deploy-patterns]] — The same session required a `vercel promote` workaround to deploy the fix

## Sources

- [[daily/2026-04-29.md]] — Session at 00:19: "100% of all quizzes (113 mcq_single + 50 mcq_multi) were silently broken, not just True/False"
- [[daily/2026-04-29.md]] — Session at 00:19: "Changed option-ID validation from `uuidSchema` to `z.string().min(1).max(64)` — option IDs are arbitrary strings by design"
- [[daily/2026-04-29.md]] — Session at 00:19: "Zod schema mismatch caused `validateAnswerForQuestion` to return `{ok: false}` for every answer — scoring every quiz 0"
- [[daily/2026-04-29.md]] — Session at 00:19: "DB-wide audit (`WHERE NOT (id ~ uuid_regex)`) revealed the scope was 100% of questions, not just True/False"
- [[daily/2026-04-29.md]] — Session at 00:19: "Leaderboard filtered to exclude 0 XP users; quiz feedback tick/cross icon overlap fixed on mobile"
