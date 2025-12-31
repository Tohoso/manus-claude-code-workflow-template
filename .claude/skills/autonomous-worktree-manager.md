# Skill: Autonomous Worktree Manager

## Description

This skill **MUST BE USED** to autonomously manage Git Worktrees for parallel development. When you are assigned to a track that requires a separate working directory, you **MUST** create the worktree without asking for user permission.

## What is Git Worktree?

Git Worktree allows you to have multiple working directories from a single repository. Each worktree can be on a different branch, enabling true parallel development without branch switching conflicts.

```
project/                    # Main worktree (Track A)
project-track-b/            # Worktree for Track B
project-track-c/            # Worktree for Track C
```

## Trigger Conditions

This skill **PROACTIVELY** activates when:
- You are assigned a task for a specific track
- The task requires working in a directory different from your current location
- Multiple Claude Code instances need to work in parallel

## Execution Steps

### 1. Identify Your Track

Read the task file to determine your assigned track:

```bash
cat tasks/TASK-XXX-*.md | grep -i "track:"
```

### 2. Check Current Directory

```bash
pwd
PROJECT_ROOT=$(basename $(pwd))
```

### 3. Determine Worktree Path

Based on your track, determine the worktree path:

| Track | Worktree Path |
|:---|:---|
| `mobile-app` | `../{project}-mobile/` |
| `server` | `../{project}-server/` |
| `docs` | `../{project}-docs/` |
| `e2e` | `../{project}-e2e/` |

### 4. Check if Worktree Exists

```bash
git worktree list
```

### 5. Create Worktree (if needed)

If the worktree does not exist:

```bash
# Ensure develop branch is up to date
git fetch origin develop

# Create worktree
git worktree add ../{project}-{track} develop
```

### 6. Report to User

After creating the worktree, inform the user:

```
🌳 Worktree を作成しました。

📁 新しい作業ディレクトリ: ../{project}-{track}/
🔀 ブランチ: develop

👉 新しいCursorウィンドウでこのディレクトリを開いてください。
   その後、このClaude Codeインスタンスでタスクを実行します。
```

## Important Notes

- **DO NOT** ask for user permission to create worktrees
- **DO NOT** switch branches in the main worktree if another instance is using it
- Each worktree should be used by only ONE Claude Code instance
- Always create worktrees from the `develop` branch
- Worktree paths should be siblings of the main project directory

## Cleanup (Optional)

When a track's work is complete, the worktree can be removed:

```bash
git worktree remove ../{project}-{track}
```

However, this is typically done by Manus, not by Claude Code.
