# Skill: Progress Tracker

## Description

This skill **MUST BE USED** to update `progress.md` whenever you start or complete a task. This file is the **single source of truth** for project status and must always be kept up to date.

## Trigger Conditions

This skill **PROACTIVELY** activates when:
- You begin working on a new task (status → 🟡 In Progress)
- You complete a task (status → ✅ Done)
- You encounter a blocker (status → 🔴 Blocked)
- You create a PR (add PR number to the table)

## Execution Steps

### 1. Read Current Progress

```bash
cat progress.md
```

### 2. Update Task Status

Edit `progress.md` to update the relevant task row:

**When starting a task:**
```markdown
| TASK-001 | Task description | Track A | Claude-1 | 🟡 In Progress | - |
```

**When completing a task:**
```markdown
| TASK-001 | Task description | Track A | Claude-1 | ✅ Done | #5 |
```

**When blocked:**
```markdown
| TASK-001 | Task description | Track A | Claude-1 | 🔴 Blocked | - |
```

### 3. Update Summary Metrics

Update the "Overall Status" section:

```markdown
## Overall Status

| Metric | Value |
|:---|:---|
| **Total Tasks** | 9 |
| **Completed** | 5 |
| **In Progress** | 2 |
| **Blocked** | 0 |
```

### 4. Update Active Tracks

```markdown
## Active Tracks

| Track | Owner | Current Task | Status |
|:---|:---|:---|:---|
| Mobile App | Claude-1 | TASK-004 | 🟡 Working |
| PC Server | Claude-2 | TASK-006 | 🟡 Working |
```

### 5. Update Timestamp

Always update the "Last Updated" line at the top:

```markdown
> **Last Updated**: 2025-01-15 14:30 (by Claude-1)
```

### 6. Commit the Update

```bash
git add progress.md
git commit -m "chore: update progress for TASK-XXX"
```

## Status Legend

| Symbol | Meaning | When to Use |
|:---|:---|:---|
| ⚪ | Not Started | Task has not been picked up yet |
| 🟡 | In Progress | You are actively working on this task |
| ✅ | Done | Task is complete and PR is merged |
| 🔴 | Blocked | Cannot proceed due to dependency or issue |
| ⏸️ | On Hold | Temporarily paused (e.g., waiting for feedback) |

## Important Notes

- **ALWAYS** update progress.md before and after working on a task
- **NEVER** mark a task as ✅ Done until the PR is merged
- If you encounter a conflict in progress.md, **STOP** and notify the user
- Manus will resolve progress.md conflicts as the orchestrator
