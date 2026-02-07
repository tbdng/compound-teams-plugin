# Compound Teams

Agent Teams + Ralph Loops + Compound Learning — a coding factory workflow for Claude Code.

## What It Does

Combines three ideas into one workflow:

1. **Agent Teams** — spawn coordinated teammates that work in parallel, communicate peer-to-peer, and claim tasks from a shared list
2. **Ralph Loops** — every teammate iterates (implement → test → fix → retest) until all checks pass, never shipping broken code
3. **Compound Learning** — after each cycle, learnings get captured in CLAUDE.md so future sessions start smarter

## Install

```
/plugin marketplace add https://github.com/tbdng/compound-teams-plugin
/plugin install compound-teams
```

Then in any project:
```
/ct:setup
```

This safely installs the workflow into your existing project without overwriting anything.

## The Loop

```
/ct:plan  →  /ct:build  →  /ct:review  →  /ct:compound  →  repeat
```

| Command | What it does |
|---------|-------------|
| `/ct:setup` | Safe install into existing projects. Detects your stack, enables Agent Teams, augments CLAUDE.md |
| `/ct:plan` | Research codebase + web, create task-by-task implementation plan |
| `/ct:build` | Spawn Agent Team. Each teammate runs Ralph Loops until tests pass |
| `/ct:review` | 3 specialized reviewers (security, architecture, correctness) |
| `/ct:quick` | Fast build for small tasks — 1 builder + 1 verifier |
| `/ct:compound` | Extract learnings into CLAUDE.md for next cycle |

## Skills

| Skill | Purpose |
|-------|---------|
| `ralph-loop` | Reference for the retry-until-green pattern. Max retries, escalation rules, anti-patterns |
| `compound-learning` | How to write good CLAUDE.md entries. Format, sections, maintenance |

## How /ct:build Works

1. Reads your plan from `.claude/plans/`
2. Spawns an Agent Team (builders + QA)
3. Creates tasks in the shared task list with dependencies
4. Each builder follows the Ralph Loop:
   - Pick task → implement → test → fail? fix and retry → pass? lint → typecheck → commit → next task
5. QA runs full test suite on completed tasks, creates fix tasks if needed
6. Team lead monitors, unblocks stuck teammates, shuts down runaway loops
7. Final test pass → clean up → report

## Cost Awareness

Agent Teams use more tokens than single-agent work. Each teammate has its own context window. The Ralph Loop adds iterations. Built-in safeguards prevent runaway costs:

- Max 5 retries per unique error
- Stuck teammates escalate after 3 attempts
- Team lead shuts down teammates looping without progress

## Tips

- **Always start with `/ct:plan`** — good plans = fast builds
- **Run `/ct:compound` after every meaningful session** — this is what makes it compound
- **Use tmux split-pane mode** to watch all teammates simultaneously
- **Use delegate mode** (Shift+Tab) during builds — lead coordinates, doesn't code
- **Use `/ct:quick` for small stuff** — don't over-engineer a bug fix

## License

MIT
