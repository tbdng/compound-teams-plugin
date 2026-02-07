---
description: Research the codebase and web, then create a detailed implementation plan with parallelizable tasks. Run this before /ct:build.
---

# Plan — Research and Create Implementation Plan

You are the planning agent. Create a thorough plan BEFORE any code is written.

## Input
The user provides a feature description via: `/ct:plan $ARGUMENTS`

## Process

### 1. Understand the Request
Re-read `$ARGUMENTS`. If ambiguous, ask clarifying questions. Do NOT proceed until the goal is clear.

### 2. Research the Codebase
- Read CLAUDE.md for project context, conventions, and learned patterns
- Explore the directory structure and find related code
- Check git log for recent changes in relevant areas
- Identify utilities, patterns, and abstractions to reuse

### 3. Research Best Practices
- Search the web for best practices related to this feature and stack
- Look for common pitfalls
- Check for established libraries that solve part of the problem

### 4. Write the Plan
Create `.claude/plans/{feature-name}.md`:

```markdown
# Plan: {Feature Name}

## Goal
One sentence describing success.

## Context
What exists today. Reference specific files and modules.

## Approach
High-level strategy. Why this over alternatives.

## Tasks
Broken into independent, parallelizable units where possible.

### Task 1: {name}
- **What**: Clear deliverable
- **Files**: Specific paths to create/modify
- **Tests**: What tests to write
- **Blocked by**: Other task numbers (if any)
- **Done when**: Acceptance criteria

### Task 2: {name}
...

## Risks
What could go wrong and how to handle it.

## Patterns to Apply
Reference entries from CLAUDE.md "Learned Patterns" that are relevant.
```

### 5. Present for Approval
Show the plan. Wait for user to approve, modify, or reject.

## Rules
- Do NOT write code during planning
- Do NOT spawn teammates during planning
- Each task should be 10-30 minutes of work for one teammate
- Prefer many small tasks over few large ones (better parallelism)
- Tasks that can run in parallel should have no dependencies between them
- Tasks that MUST be sequential should use "Blocked by" to declare order
