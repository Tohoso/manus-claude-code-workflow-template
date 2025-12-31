# Skill: Manus Delegator

## Description

This skill **MUST BE USED** when you encounter a situation that requires Manus's intervention. Manus is the orchestrator responsible for design decisions, research, architecture changes, and conflict resolution.

## Trigger Conditions

This skill **PROACTIVELY** activates when:
- You need a **design decision** that affects multiple components
- You need **research** on external APIs, libraries, or best practices
- You encounter an **architecture question** not covered in existing docs
- You find a **bug or issue** that requires broader context to fix
- You need **clarification** on requirements or specifications
- You encounter a **merge conflict** in shared files (especially `progress.md`)

## Execution Steps

### 1. Create Request File

Create a file in `tasks/` with the naming convention:

```
tasks/MANUS-REQUEST-{timestamp}.md
```

Example:
```bash
TIMESTAMP=$(date +%Y%m%d-%H%M%S)
touch tasks/MANUS-REQUEST-${TIMESTAMP}.md
```

### 2. Write Request Content

Use this template:

```markdown
# Manus Request

**From**: Claude-{N} ({Track Name})
**Date**: YYYY-MM-DD HH:MM
**Priority**: High / Medium / Low
**Type**: Design Decision / Research / Architecture / Bug / Clarification

---

## Context

[Describe the current situation and what you're working on]

## Question / Request

[Clearly state what you need from Manus]

## Options Considered (if applicable)

1. **Option A**: [Description]
   - Pros: ...
   - Cons: ...

2. **Option B**: [Description]
   - Pros: ...
   - Cons: ...

## Impact

[Describe what is blocked or affected by this decision]

## Suggested Approach (if any)

[Your recommendation, if you have one]

---

**Status**: ⏳ Awaiting Response
```

### 3. Commit and Push

```bash
git add tasks/MANUS-REQUEST-*.md
git commit -m "chore: request Manus decision on {topic}"
git push origin $(git branch --show-current)
```

### 4. Notify User

Inform the user immediately:

```
🔔 Manusへのリクエストを作成しました。

📄 ファイル: tasks/MANUS-REQUEST-{timestamp}.md
📋 内容: {Brief description}
⏳ ステータス: 回答待ち

👉 Manusにこのリクエストを確認するよう伝えてください。
```

### 5. Wait for Response

Manus will respond by creating:
```
tasks/MANUS-RESPONSE-{timestamp}.md
```

Once you see this file, read it and continue your work.

## Response Format (from Manus)

Manus will respond with:

```markdown
# Manus Response

**To**: Claude-{N} ({Track Name})
**Date**: YYYY-MM-DD HH:MM
**Re**: MANUS-REQUEST-{timestamp}

---

## Decision / Answer

[Manus's decision or answer]

## Rationale

[Explanation of why this decision was made]

## Action Items

1. [What Claude should do next]
2. [Any follow-up tasks]

---

**Status**: ✅ Resolved
```

## Important Notes

- **DO NOT** make major design decisions without consulting Manus
- **DO NOT** block indefinitely; if urgent, notify user to escalate
- **ALWAYS** provide context and options when possible
- Manus may respond via the file system OR directly through the user
