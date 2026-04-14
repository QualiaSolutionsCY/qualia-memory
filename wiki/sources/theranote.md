---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/theranote
---

# theranote

> Turborepo monorepo with TheraNote (clinical documentation) and ThriveSync (operations management) for preschool special-education programs

## Overview

TheraNote/ThriveSync is a Turborepo monorepo containing two apps for preschool special-education programs. TheraNote (port 3000) handles clinical documentation for therapists including SOAP notes, goals, and compliance. ThriveSync (port 3001) manages operations for administrators including staff, classrooms, and finance. The project is 91% complete and MVP demo-ready.

## Tech Stack

- Turborepo monorepo with pnpm workspaces
- Next.js, React, TypeScript
- Supabase (PostgreSQL, Auth)
- Vercel (deployment — two apps)

## Status

- **Language:** TypeScript
- **Last updated:** 2026-03-03
- **Visibility:** Private

## Key Features

- **TheraNote app:** Clinical documentation, SOAP notes, therapy goals, compliance tracking
- **ThriveSync app:** Staff management, classroom management, finance operations
- Monorepo with shared packages (database types)
- Dual Vercel deployments (theranote-delta.vercel.app, thrivesync-lac.vercel.app)
- 91% complete, MVP demo-ready
- BMad method workflow integration

## Related

- [[shai]] (related therapy platform)
- [[theracoach]] (related speech therapy project)
