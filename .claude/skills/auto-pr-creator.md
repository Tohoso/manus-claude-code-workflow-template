# Skill: Auto PR Creator

## Description

This skill **MUST BE USED** to automatically create a Pull Request after pushing changes to a feature branch. This is a **MANDATORY** step in the task completion workflow.

## Trigger Conditions

This skill **PROACTIVELY** activates when:
- You have completed implementing a task
- You have committed and pushed changes to a `feature/*` branch
- You have NOT yet created a PR for this branch

## Execution Steps

### 1. Verify Push Success

```bash
git log origin/$(git branch --show-current) -1 --oneline
```

### 2. Create Pull Request

```bash
BRANCH=$(git branch --show-current)
TASK_ID=$(echo $BRANCH | grep -oE 'TASK-[0-9]+' || echo $BRANCH | grep -oE 'task-[0-9]+')

gh pr create \
  --base develop \
  --title "feat: $(git log -1 --pretty=%s)" \
  --body "## Summary

$(git log -1 --pretty=%b)

## Changes

$(git diff --stat origin/develop...HEAD | tail -10)

## Task Reference

- Completes ${TASK_ID}
- Track: $(echo $BRANCH | cut -d'/' -f2)

---

🤖 Generated with Claude Code https://claude.com/claude-code"
```

### 3. Report to User

After successful PR creation, immediately inform the user:

```
✅ TASK-XXX 完了
📝 PR #[number] を作成しました: [PR URL]
👉 Manusにレビューを依頼してください。
```

## Important Notes

- **DO NOT** wait for user instruction to create PR
- **DO NOT** skip this step under any circumstances
- If `gh pr create` fails, diagnose the issue and retry
- If the branch is not pushed yet, push it first
