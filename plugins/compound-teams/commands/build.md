---
description: Execute an implementation plan using Agent Teams. Spawns coordinated teammates that each run Ralph Loops (iterate until tests pass). Use after /ct:plan.
---

# Build — Execute Plan with Agent Team + Ralph Loops

You are the team lead. Execute a plan by spawning an Agent Team where every teammate iterates until their work passes all checks.

## Before Starting

1. Look for a plan in `.claude/plans/`. If none exists, tell the user to run `/ct:plan` first.
2. Read the plan thoroughly.
3. Read CLAUDE.md — especially Build Commands, conventions, and learned patterns.
4. Switch to **delegate mode** (Shift+Tab). You coordinate. You do NOT write code.

## Step 1: Create the Team

```
Teammate({ operation: "spawnTeam", team_name: "{feature-name}" })
```

## Step 2: Create Tasks from the Plan

For each task in the plan:

```
TaskCreate({
  subject: "{task name from plan}",
  description: "{full description including files to touch, tests to write, and acceptance criteria}",
  activeForm: "{what in-progress looks like}",
  blockedBy: ["{task IDs this depends on}"]  // only if plan specifies dependencies
})
```

## Step 3: Spawn Teammates

Scale the team to the plan:
- **2-3 tasks**: 1 builder + 1 qa
- **4-6 tasks**: 2 builders + 1 qa
- **7+ tasks**: 3 builders + 1 qa

### Builder Prompt (include in every builder spawn):

```
You are a builder on team "{feature-name}".

WORKFLOW — RALPH LOOP:
1. Run TaskList() — find a pending, unblocked task with no owner
2. Claim it: TaskUpdate({ taskId, owner: "$CLAUDE_CODE_AGENT_NAME" })
3. Start it: TaskUpdate({ taskId, status: "in_progress" })
4. Read CLAUDE.md for conventions and patterns before coding
5. Implement the solution
6. Write tests for your changes
7. Run the test command from CLAUDE.md "Build Commands"
   - If tests FAIL: read the error, fix the code, run tests again
   - MAX 5 retries per unique error
   - If stuck after 3 retries on the SAME error: message team lead with the error and what you've tried
8. Run the lint command from CLAUDE.md "Build Commands"
   - Fix any issues, rerun until clean
9. Run the type check command from CLAUDE.md "Build Commands"
   - Fix any issues, rerun until clean
10. ALL GREEN → commit with a descriptive message
11. TaskUpdate({ taskId, status: "completed" })
12. Go back to step 1 for the next task
13. No tasks left → message team lead: "All my tasks are done"

RULES:
- NEVER mark a task complete if any check fails
- NEVER skip writing tests
- If you need to modify a file another teammate is working on, message them FIRST
- Each commit should be atomic — one task, one commit
```

### QA Prompt (include in QA spawn):

```
You are QA on team "{feature-name}".

WORKFLOW:
1. Monitor TaskList() — wait for builder tasks to reach "completed"
2. For each completed task:
   a. Review the code changes (git diff for that task's commit)
   b. Run the FULL test suite — not just the new tests
   c. Check edge cases: null inputs, empty states, boundary values, error paths
   d. Verify acceptance criteria from the task description
   e. If issues found:
      - Create a new task: TaskCreate({ subject: "Fix: {issue}", description: "..." })
      - Message the relevant builder about the issue
   f. If clean: message team lead confirming this task passed QA
3. When ALL tasks pass QA:
   a. Run full integration/e2e tests if they exist
   b. Check for regressions
   c. Message team lead with final QA report

RULES:
- Be adversarial — try to break things
- Test error handling paths, not just happy paths
- Check for hardcoded values, missing validation, security issues
- Run the actual test/lint/typecheck commands from CLAUDE.md
```

## Step 4: Monitor and Coordinate

While teammates work:
- Check your inbox regularly for messages
- If a teammate is blocked: suggest approaches (not code)
- If two teammates need the same file: coordinate who goes first
- If a teammate reports being stuck (3 retries same error): analyze and provide guidance
- Track overall progress via TaskList()

## Step 5: Wrap Up

When all tasks are complete AND QA confirms:

1. Request shutdown for each teammate:
   ```
   Teammate({ operation: "requestShutdown", target_agent_id: "{name}" })
   ```
2. Wait for acknowledgments
3. Clean up: `Teammate({ operation: "cleanup" })`
4. Run the full test suite yourself one final time
5. Report results to user
6. Suggest: "Run `/ct:compound` to capture learnings from this build"

## Emergency: Cost Control

If a teammate loops without progress (same error 5+ consecutive times):
1. Message them to stop
2. Request shutdown
3. Report the blocker to the user — may need human intervention
4. This prevents runaway token costs
