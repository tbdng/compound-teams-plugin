---
description: Extract learnings from the most recent build/review cycle and update CLAUDE.md. This is what makes the workflow compound — each cycle improves the next.
---

# Compound — Extract and Document Learnings

Analyze what just happened and update CLAUDE.md so future sessions benefit.

## Process

### 1. Gather Context

```bash
# Recent commits
git log --oneline -20

# Check for plans
ls .claude/plans/*.md 2>/dev/null

# Current CLAUDE.md
cat CLAUDE.md
```

### 2. Identify Learnings

Look for:

**Patterns that worked:**
- Code patterns that solved problems well
- Libraries or approaches that proved effective
- Task decomposition that enabled good parallelism

**Patterns that failed:**
- Approaches tried and abandoned
- Libraries that didn't work as expected
- Wrong assumptions

**Recurring bugs:**
- Errors that appeared repeatedly during Ralph loops
- Common mistakes teammates made
- Things the linter/type checker caught repeatedly

**Performance insights:**
- Optimizations that helped
- Caching strategies
- Query patterns to prefer or avoid

### 3. Update CLAUDE.md

Append entries to the "Learned Patterns" sections. Each entry MUST be:

- **Dated**: `[YYYY-MM-DD]`
- **Specific**: reference actual files, functions, or error messages
- **Actionable**: future Claude should know what to DO differently

✅ Good: `[2026-02-07] Stripe webhook routes need raw body — add export const config = { api: { bodyParser: false } } in Next.js App Router, or the signature check fails with "No signatures found matching the expected signature".`

❌ Bad: `[2026-02-07] Be careful with webhooks`

### 4. Update Build Commands (if needed)

If the test/lint/typecheck commands changed or new ones were discovered, update the "Build Commands" section.

### 5. Update Architecture (if needed)

If the project structure changed (new directories, new major modules), update accordingly.

### 6. Archive Plan

```bash
# Move completed plan to archive
mv .claude/plans/{feature}.md .claude/plans/archive/{feature}.md 2>/dev/null
```

### 7. Report

Tell the user:
- How many new learnings were added
- Any patterns that contradict previous ones (update, don't duplicate)
- Suggestions for what to build or fix next

## Maintenance Rules
- Don't duplicate entries — update if the learning evolved
- Remove entries for tech that was removed
- Keep entries concise — this file is read every session
- If CLAUDE.md exceeds ~500 lines, suggest splitting into topic files that CLAUDE.md references
