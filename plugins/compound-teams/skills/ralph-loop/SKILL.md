---
name: ralph-loop
description: The Ralph Loop pattern for Agent Team teammates. Use when spawning builder teammates that need to iterate until all tests, lint, and type checks pass. Provides the standard retry logic, max attempts, and escalation rules.
---

# Ralph Loop — Iterate Until Green

The Ralph Loop is a persistent retry pattern for AI coding agents. Instead of writing code and hoping it works, the agent implements → tests → fixes → retests in a loop until everything passes.

## The Pattern

```
implement → test → [FAIL?] → read error → fix → test again → [PASS?] → lint → [FAIL?] → fix → lint again → [PASS?] → typecheck → [FAIL?] → fix → typecheck again → [PASS?] → DONE
```

## Rules

1. **Max 5 retries per unique error.** If the same test fails 5 times with the same root cause, stop and escalate.

2. **Max 3 retries before escalating.** If stuck on the same error after 3 attempts, message the team lead with:
   - The error message
   - What you've tried
   - Your hypothesis about why it's failing

3. **Run checks in order:** tests → lint → typecheck. Don't skip ahead.

4. **Each retry should be different.** Don't repeat the same fix. If approach A failed, try approach B. If both fail, try a fundamentally different strategy.

5. **Read the error message carefully.** Most failures contain the answer. Parse stack traces, find the actual line, understand the assertion.

## Verification Commands

Read these from CLAUDE.md "Build Commands" section. Common patterns:

| Stack | Tests | Lint | Typecheck |
|-------|-------|------|-----------|
| Next.js/React | `npm test` or `npx vitest` | `npm run lint` | `npx tsc --noEmit` |
| Python | `pytest` | `ruff check .` | `mypy .` |
| Node.js | `npm test` | `npx eslint .` | `npx tsc --noEmit` |
| Go | `go test ./...` | `golangci-lint run` | (built into compiler) |

## When to Break the Loop

Stop looping and escalate when:
- Same error persists after 3 different fix attempts
- The fix requires changes outside your assigned task scope
- You need a dependency or config change that affects other teammates
- The test itself appears to be wrong (explain why to the team lead)

## Anti-Patterns

- **Don't suppress errors** to make tests pass. Fix the root cause.
- **Don't delete failing tests.** Fix the code or fix the test, but don't remove coverage.
- **Don't retry without changing anything.** Each attempt must try something different.
- **Don't fix unrelated tests.** If a pre-existing test fails, note it and move on — it's not your task.
