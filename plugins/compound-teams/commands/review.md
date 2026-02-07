---
description: Spawn a review team that examines recent code changes from security, architecture, and correctness perspectives. Run after /ct:build.
---

# Review — Multi-Perspective Code Review Team

Spawn 3 specialized reviewers to examine code changes from different angles.

## What to Review

If the user provides a branch/PR/commit range with `$ARGUMENTS`, use that.
Otherwise, review changes from the most recent build: `git diff main...HEAD`

## Step 1: Gather the Diff

```bash
git diff main...HEAD --stat
git log --oneline main..HEAD
```

Read the diff before spawning reviewers.

## Step 2: Spawn Review Team

```
Teammate({ operation: "spawnTeam", team_name: "review-{feature}" })
```

Create 3 tasks, one per reviewer lens. Then spawn 3 teammates:

### Security Reviewer Prompt:
```
Review the git diff (main...HEAD) for security issues.

CHECK: SQL injection, XSS, CSRF, hardcoded secrets/keys, missing input validation, insecure auth patterns, sensitive data in logs/errors, missing rate limiting, unsafe deserialization, path traversal.

Message team-lead with findings: file, line, severity (critical/high/medium/low), suggested fix.
If nothing found, confirm the changes look secure.
```

### Architecture Reviewer Prompt:
```
Review the git diff (main...HEAD) for architectural quality. Read CLAUDE.md first for project conventions.

CHECK: Pattern consistency, separation of concerns, SOLID violations, DRY without over-abstraction, dependency direction, testability, performance at scale, error handling consistency.

Message team-lead with findings: file, concern, suggestion.
Also note things done WELL — we document good patterns too.
```

### Correctness Reviewer Prompt:
```
Review the git diff (main...HEAD) for correctness. Also run the test suite.

CHECK: Test quality (not coverage theater), edge cases (null/empty/boundary), helpful error messages, type consistency across API boundaries, race conditions, resource cleanup (connections closed, listeners removed), acceptance criteria met.

Message team-lead with findings.
```

## Step 3: Synthesize

Collect all findings. Present unified review:

```
## Code Review Summary

### Critical (must fix before merge)
- ...

### Improvements (should fix)
- ...

### Suggestions (nice to have)
- ...

### Good Patterns (add to CLAUDE.md via /ct:compound)
- ...
```

## Step 4: Clean Up

Shut down all reviewers. Clean up the team.

If critical or "should fix" issues exist, suggest creating a fix plan:
"Run `/ct:plan Fix review findings` to address these issues."
