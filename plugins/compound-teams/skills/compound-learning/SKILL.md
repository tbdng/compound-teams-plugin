---
name: compound-learning
description: Knowledge management for compound engineering. Use when adding learnings to CLAUDE.md, reviewing past patterns, or deciding how to structure project memory. Covers entry format, section organization, and maintenance.
---

# Compound Learning — Project Memory Management

The compound learning system turns each build/review cycle into accumulated project knowledge via CLAUDE.md.

## How It Works

Every project has a CLAUDE.md that Claude reads at the start of each session. The `/ct:compound` command appends learnings after each cycle. Over time, Claude becomes increasingly effective at working in this specific codebase.

## Entry Format

Every learning entry must be:

### Dated
```
[2026-02-07] Always use server actions for mutations in Next.js App Router
```

### Specific
```
[2026-02-07] The Stripe webhook handler at /app/api/webhooks/stripe/route.ts
needs `export const config = { api: { bodyParser: false } }` — without raw
body, constructEvent() throws "No signatures found"
```

### Actionable
Future Claude should know what to DO, not just what happened:

✅ "Use `prisma.$transaction()` when updating both the user and subscription tables"
❌ "Had a bug with Prisma transactions"

## Section Structure

CLAUDE.md should have these sections (created by `/ct:setup`):

### What Works
Patterns, libraries, approaches that proved effective:
```
[2026-02-07] Zod schemas as single source of truth: define once in /lib/schemas/,
use for API validation, form validation, and TypeScript types via z.infer<>
```

### What Failed
Approaches tried and abandoned, with reasons:
```
[2026-02-07] tRPC added too much boilerplate for our API surface (3 endpoints).
Switched to plain route handlers with Zod validation. Revisit if API grows past 15 endpoints.
```

### Recurring Bugs
Errors that show up repeatedly — prevent them proactively:
```
[2026-02-08] Forgetting "use server" directive causes: "Error: Functions cannot be
passed directly to Client Components unless you explicitly expose it by marking it
with 'use server'." Always add to files in /app/actions/.
```

## Maintenance

### Deduplication
If a new learning overlaps with an existing one, UPDATE the existing entry rather than adding a duplicate. Add the new date.

### Retirement
Remove entries about tech that's been removed from the project. Dead patterns create noise.

### Size Management
If CLAUDE.md exceeds ~500 lines:
1. Split into topic files: `.claude/docs/auth-patterns.md`, `.claude/docs/api-conventions.md`
2. Reference them from CLAUDE.md: "See `.claude/docs/auth-patterns.md` for auth conventions"
3. Keep CLAUDE.md as the index with the most critical patterns inline

### Contradiction Resolution
If a new learning contradicts an old one, update the old entry:
```
[2026-02-07] ~~Use REST for all endpoints~~ [2026-02-15] Use REST for CRUD,
WebSockets for real-time features (chat, notifications). See /lib/ws/ for setup.
```

## What Makes Good Compound Learnings

| Good | Bad |
|------|-----|
| References specific files/functions | Vague generalities |
| Includes error messages | "Had an error" |
| Says what TO DO | Says what happened |
| Includes WHY | Just states facts |
| Has context for when it applies | Universal claims |
