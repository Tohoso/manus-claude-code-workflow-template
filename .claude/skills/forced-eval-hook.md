# Skill: Forced Eval Hook

## Description

This skill ensures that all other skills in this project are **reliably evaluated and activated**. Research has shown that Claude Code skills have a natural activation rate of only 20-50%. This hook increases that rate to 84%+ by explicitly checking skill applicability at key decision points.

## Trigger Conditions

This skill **MUST BE EVALUATED** at these checkpoints:

1. **Task Start**: When you begin working on any task
2. **Pre-Commit**: Before committing any changes
3. **Post-Push**: After pushing to remote
4. **Blocker Encountered**: When you cannot proceed
5. **Task Complete**: When you finish a task

## Execution Steps

At each checkpoint, run through this checklist:

### Checkpoint: Task Start

```
□ Have I read CLAUDE.md?
□ Have I checked progress.md for current status?
□ Have I identified my track from the task file?
□ Do I need to create a worktree? → autonomous-worktree-manager
□ Have I updated progress.md to 🟡 In Progress? → progress-tracker
```

### Checkpoint: Pre-Commit

```
□ Have I updated progress.md? → progress-tracker
□ Are there any design decisions needed? → manus-delegator
□ Is my commit message following conventions?
```

### Checkpoint: Post-Push

```
□ Have I created a PR? → auto-pr-creator
□ Have I notified the user about the PR?
```

### Checkpoint: Blocker Encountered

```
□ Is this a design/architecture question? → manus-delegator
□ Is this a merge conflict? → Notify user, Manus will resolve
□ Have I updated progress.md to 🔴 Blocked? → progress-tracker
```

### Checkpoint: Task Complete

```
□ Is progress.md updated to ✅ Done? → progress-tracker
□ Is the PR created and linked? → auto-pr-creator
□ Have I notified the user?
```

### Checkpoint: E2E Testing Phase (Manus Only)

```
□ Am I about to modify code directly? → STOP! Create BUG-FIX task instead
□ Have I created a BUG-FIX-{timestamp}.md file for each bug found?
□ Have I delegated the fix to the appropriate Claude Code instance?
□ Am I waiting for Claude Code's PR before proceeding?
```

> **Critical Rule**: During E2E testing, Manus must NEVER directly modify code.
> All bug fixes must be delegated to Claude Code via BUG-FIX task files.
> See: `docs/e2e-bugfix-flow.md`

## Skill Activation Keywords

When you see these keywords in your context, **immediately activate** the corresponding skill:

| Keyword | Skill to Activate |
|:---|:---|
| "create PR", "pull request", "push完了" | `auto-pr-creator` |
| "worktree", "parallel", "別トラック" | `autonomous-worktree-manager` |
| "progress", "status", "進捗" | `progress-tracker` |
| "design decision", "architecture", "Manusに確認" | `manus-delegator` |
| "E2E", "bug found", "バグ発見", "integration test" | `e2e-bugfix-flow` |

## Important Notes

- This skill is a **meta-skill** that ensures other skills fire correctly
- Run through the appropriate checklist at EVERY checkpoint
- If unsure whether a skill applies, **assume it does** and check
- This dramatically improves workflow consistency and reduces human intervention
