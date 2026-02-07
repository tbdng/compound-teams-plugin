---
description: Safely install compound-teams workflow into an existing project. Detects existing CLAUDE.md, settings, and commands — never overwrites, only augments.
---

# Setup — Install Compound Teams Workflow

You are the setup assistant. Install the compound-teams workflow (Plan → Build → Review → Compound) into this project safely.

## Step 1: Audit Current State

Before changing anything, check what exists:

```bash
echo "=== CLAUDE.md ==="
ls -la CLAUDE.md .claude/CLAUDE.md 2>/dev/null || echo "No CLAUDE.md found"

echo "=== Settings ==="
cat .claude/settings.json 2>/dev/null || echo "No settings.json"

echo "=== Existing commands ==="
ls .claude/commands/ 2>/dev/null || echo "No commands directory"

echo "=== Existing agents ==="
ls .claude/agents/ 2>/dev/null || echo "No agents directory"

echo "=== Git status ==="
git status --short 2>/dev/null | head -5 || echo "Not a git repo"
```

**Report findings to the user before proceeding.**

## Step 2: Enable Agent Teams

Check `.claude/settings.json`:
- If it **doesn't exist**: create it with `{ "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" }`
- If it **exists but lacks the flag**: add the flag, preserving all other settings
- If it **already has the flag**: skip, tell user it's already enabled

**Show the user the change before writing.**

## Step 3: Detect Project Stack

```bash
echo "=== Package manager ==="
ls package.json pnpm-lock.yaml yarn.lock bun.lock 2>/dev/null

echo "=== Framework ==="
cat package.json 2>/dev/null | grep -E '"(next|react|vue|svelte|astro|express|fastify|hono)"' | head -5
cat pyproject.toml 2>/dev/null | head -20
cat go.mod 2>/dev/null | head -5

echo "=== Test runner ==="
ls jest.config* vitest.config* playwright.config* cypress.config* pytest.ini 2>/dev/null
cat package.json 2>/dev/null | grep -E '"test"' | head -1

echo "=== Linter ==="
ls .eslintrc* eslint.config* biome.json .ruff.toml ruff.toml 2>/dev/null

echo "=== Type checker ==="
ls tsconfig.json mypy.ini 2>/dev/null

echo "=== CI ==="
ls .github/workflows/*.yml .github/workflows/*.yaml 2>/dev/null | head -3
```

Report: "I detected [framework], [test runner], [linter], [type checker]. The Ralph Loop will use these for verification."

## Step 4: Augment CLAUDE.md

**NEVER overwrite existing CLAUDE.md.** Check if compound-teams sections already exist.

If the file doesn't exist, create a minimal one. If it exists, **append only the missing sections** to the end:

```markdown

---

## Compound Teams Workflow

This project uses: `/ct:plan` → `/ct:build` → `/ct:review` → `/ct:compound`

### Ralph Loop (All Teammates Follow This)
1. Implement the solution
2. Write or update tests
3. Run tests — if fail: read error, fix, retry (max 5 attempts per error)
4. Run linter/type checker — if fail: fix and retry
5. Only mark task complete when ALL checks pass
6. If stuck after 3 retries on same error, message team lead with blocker

### Build Commands
- Tests: `[DETECTED_TEST_COMMAND]`
- Lint: `[DETECTED_LINT_COMMAND]`
- Type check: `[DETECTED_TYPECHECK_COMMAND]`
- Dev server: `[DETECTED_DEV_COMMAND]`

### Learned Patterns
<!-- Grows via /ct:compound — each entry is dated and actionable -->

#### What Works

#### What Failed

#### Recurring Bugs
```

Replace the `[DETECTED_*]` placeholders with the actual commands from Step 3.

**Show the user exactly what will be appended. Get confirmation before writing.**

## Step 5: Create Plans Directory

```bash
mkdir -p .claude/plans/archive
```

## Step 6: Summary

```
✅ Compound Teams installed!

Commands available (via plugin):
  /ct:setup     — You just ran this
  /ct:plan      — Research and plan a feature
  /ct:build     — Execute with Agent Team + Ralph loops
  /ct:review    — Multi-perspective code review
  /ct:quick     — Fast builds for small tasks
  /ct:compound  — Extract learnings into CLAUDE.md

Agent Teams: enabled
Plans directory: .claude/plans/
Stack detected: [framework] + [test runner] + [linter]

Start with: /ct:plan <your next feature>
```

## Safety Rules
- NEVER overwrite existing files — only append or create new
- ALWAYS show changes to user before writing
- PRESERVE all existing CLAUDE.md content
- If a conflict is found, present both versions and let user choose
