# CLAUDE.md - Project Configuration for Claude Code

## Project Overview

**Project Name**: [YOUR_PROJECT_NAME]
**Description**: [YOUR_PROJECT_DESCRIPTION]
**Repository**: [YOUR_REPOSITORY_URL]

---

## 🎯 Workflow: Manus × Claude Code Collaboration

This project uses a collaborative workflow where **Manus** acts as the orchestrator and **Claude Code** handles implementation.

### Role Definitions

| Role | Agent | Responsibilities |
|:---|:---|:---|
| **Orchestrator** | Manus | Requirements, architecture, task creation, code review, conflict resolution |
| **Implementer** | Claude Code | Task implementation, testing, progress updates, PR creation |

---

## 📋 MANDATORY: Task Execution Flow

When you receive a task, you **MUST** follow this exact flow:

### Step 1: Environment Check
```bash
pwd  # Check current directory
git status  # Check branch status
```

### Step 2: Identify Your Track
Read the task file in `tasks/` to identify your assigned track (e.g., `mobile-app`, `server`, `docs`).

### Step 3: Setup Worktree (if needed)
If you are NOT in the correct worktree for your track, use the `autonomous-worktree-manager` skill to create one.

### Step 4: Create Feature Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/{track}/{task-id}-{short-description}
```

### Step 5: Implement
- Follow the task requirements
- Write clean, well-documented code
- Add tests where appropriate

### Step 6: Update Progress
Edit `progress.md` to mark your task as `🟡 In Progress` then `✅ Done`.

### Step 7: Commit and Push
```bash
git add .
git commit -m "feat: {description} (TASK-XXX)"
git push origin feature/{track}/{task-id}-{short-description}
```

### Step 8: Create Pull Request (MANDATORY)
**You MUST create a PR immediately after pushing. Do NOT wait for user instruction.**

```bash
gh pr create \
  --base develop \
  --title "feat: {description} (TASK-XXX)" \
  --body "## Summary
{Brief description of changes}

## Changes
- {Change 1}
- {Change 2}

## Task Reference
- Completes TASK-XXX: {Task title}
- Track: {Your track}

---
🤖 Generated with Claude Code"
```

### Step 9: Notify User
After creating the PR, inform the user:
```
TASK-XXX 完了。PR #{number} を作成しました。Manusにレビューを依頼してください。
```

---

## 🌳 AUTONOMOUS ENVIRONMENT SETUP (REQUIRED)

When starting work on a new track, you **MUST** autonomously set up your environment:

1. **Check assigned track** from task file
2. **Check current directory** with `pwd`
3. **If worktree needed**: Create it using `autonomous-worktree-manager` skill
4. **Report to user**: "Worktree created at `../{project}-{track}/`. Please open a new Cursor window there."
5. **Continue work** in the correct environment

---

## 📁 Project Structure

```
{project-root}/
├── CLAUDE.md              # This file (project configuration)
├── progress.md            # Task progress tracking (single source of truth)
├── .claude/
│   ├── agents/            # Subagent definitions
│   │   ├── main-agent.md
│   │   ├── plan-agent.md
│   │   ├── maker-agent.md
│   │   ├── test-agent.md
│   │   └── security-agent.md
│   └── skills/            # Automation skills
│       ├── auto-pr-creator.md
│       ├── progress-tracker.md
│       ├── autonomous-worktree-manager.md
│       └── manus-delegator.md
├── tasks/                 # Task definition files
│   ├── TASK-001-xxx.md
│   ├── TASK-002-xxx.md
│   └── ...
├── docs/                  # Documentation
└── src/                   # Source code
```

---

## 🔀 Branch Strategy

| Branch | Purpose | Who Creates |
|:---|:---|:---|
| `main` | Production releases | Manus (via PR merge) |
| `develop` | Integration branch | Manus |
| `feature/{track}/{task-id}-*` | Feature development | Claude Code |
| `manus/*` | Design/doc updates | Manus |

---

## ⚠️ Important Rules

1. **NEVER commit directly to `main` or `develop`**
2. **ALWAYS create PRs for your changes**
3. **ALWAYS update `progress.md` when starting/completing tasks**
4. **If you need design decisions or research**: Create `tasks/MANUS-REQUEST-{timestamp}.md` and notify user
5. **If you encounter conflicts**: Stop and notify user; Manus will resolve them

---

## 🔧 Tech Stack

[Customize this section for your project]

- **Frontend**: 
- **Backend**: 
- **Database**: 
- **Infrastructure**: 

---

## 📚 Additional Resources

- `docs/architecture.md` - System architecture
- `docs/requirements.md` - Requirements specification
- `docs/workflow/parallel-development-guide.md` - Parallel development guide
