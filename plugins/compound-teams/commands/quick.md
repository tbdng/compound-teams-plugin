---
description: Fast build for small tasks (bug fixes, refactors, config changes) that don't need a full plan. Spawns 1 builder + 1 verifier with Ralph Loop.
---

# Quick — Fast Build for Small Tasks

For tasks that don't warrant the full `/ct:plan` → `/ct:build` cycle.

## When to Use
- Bug fixes with clear repro steps
- Small refactors (rename, extract, move)
- Config or copy changes
- Adding a single test

## When NOT to Use (use /ct:plan + /ct:build)
- New features touching multiple files
- New API endpoints
- Database schema changes
- Anything needing multi-person review

## Process

User provides the task via: `/ct:quick $ARGUMENTS`

1. Read CLAUDE.md for conventions
2. Spawn a small team:

```
Teammate({ operation: "spawnTeam", team_name: "quick-fix" })
```

### Builder Prompt:
```
Implement this change: {$ARGUMENTS}

Read CLAUDE.md for conventions. Then:
1. Make the change
2. Write/update tests
3. Run tests — if fail: fix and retry (max 3 attempts)
4. Run lint + type check — fix if needed
5. Commit with descriptive message
6. Message team-lead when done
```

### Verifier Prompt:
```
Wait for the builder to finish. Then:
1. Review the git diff
2. Run the FULL test suite (not just new tests)
3. Check for regressions
4. If issues: message builder with specifics
5. If clean: message team-lead confirming
```

3. Once both confirm: shut down, clean up, report to user.

No need for `/ct:compound` unless something surprising was learned.
