# E2Eテスト時のバグ修正フロー

## 概要

このガイドでは、E2E（End-to-End）テストフェーズでバグが発見された場合の標準的な修正フローを定義します。Manus × Claude Code連携ワークフローにおいて、**Manusは直接コードを修正せず、必ずClaude Codeに委任する**という原則を徹底するためのプロセスです。

---

## 原則

| 役割 | 許可される行動 | 禁止される行動 |
|:---|:---|:---|
| **Manus** | バグの特定、タスク作成、レビュー、マージ | 直接のコード修正 |
| **Claude Code** | バグ修正の実装、テスト、PR作成 | 設計判断（Manusに委任） |

> **重要**: E2Eテスト中にバグを発見した場合、Manusは「修正が簡単そう」であっても直接コードを修正してはいけません。必ず以下のフローに従ってClaude Codeに委任してください。

---

## バグ修正フロー

### Phase 1: バグの発見と記録

Manusがテスト中にバグを発見した場合：

1. **バグの詳細を記録**
   - 発生条件
   - 期待される動作
   - 実際の動作
   - 関連するコード/ファイル（推測）

2. **バグ修正タスクファイルを作成**

```bash
# ファイル名: tasks/BUG-FIX-{timestamp}.md
```

### Phase 2: タスクファイルの作成

以下のテンプレートを使用してバグ修正タスクを作成します：

```markdown
# BUG-FIX: {バグの簡潔な説明}

## メタデータ
- **作成日時**: {YYYY-MM-DD HH:MM}
- **発見フェーズ**: E2E Testing
- **優先度**: {Critical / High / Medium / Low}
- **担当トラック**: {mobile-app / server / integration}
- **関連タスク**: {TASK-XXX}（あれば）

## バグの詳細

### 発生条件
{バグが発生する具体的な手順}

### 期待される動作
{本来あるべき動作}

### 実際の動作
{現在の誤った動作}

### 関連ファイル（推測）
- `src/xxx/yyy.ts`
- `src/zzz/www.tsx`

## 修正方針（Manusからの指示）
{修正の方向性を簡潔に記述。実装詳細はClaude Codeに任せる}

## 受け入れ条件
- [ ] バグが再現しなくなること
- [ ] 既存のテストが全てパスすること
- [ ] 新しいテストケースが追加されていること（必要に応じて）

---

## ステータス
- [ ] Claude Codeに割り当て済み
- [ ] 修正実装中
- [ ] PR作成済み
- [ ] Manusレビュー完了
- [ ] マージ完了
```

### Phase 3: Claude Codeへの委任

1. **適切なClaude Codeインスタンスを特定**
   - バグの発生箇所に基づいてトラックを決定
   - Mobile App関連 → Claude-1（Mobile Appトラック）
   - Server関連 → Claude-2（PC Serverトラック）
   - 両方に関連 → 統合担当のClaude Codeまたは両方に指示

2. **Claude Codeに指示**
   ```
   新しいバグ修正タスクがあります。
   tasks/BUG-FIX-{timestamp}.md を確認し、修正を実装してください。
   
   ブランチ名: bugfix/{track}/bug-fix-{short-description}
   
   完了後、PRを作成してManusにレビューを依頼してください。
   ```

### Phase 4: 修正の実装（Claude Code）

Claude Codeは以下のフローで修正を実装します：

```bash
# 1. 最新のdevelopを取得
git checkout develop
git pull origin develop

# 2. バグ修正ブランチを作成
git checkout -b bugfix/{track}/bug-fix-{short-description}

# 3. 修正を実装
# ... コード修正 ...

# 4. テスト実行
npm test

# 5. コミット
git add .
git commit -m "fix: {バグの説明} (BUG-FIX-{timestamp})"

# 6. プッシュ
git push origin bugfix/{track}/bug-fix-{short-description}

# 7. PR作成
gh pr create \
  --base develop \
  --title "fix: {バグの説明}" \
  --body "## バグ修正
{修正内容の説明}

## 原因
{バグの原因}

## 修正内容
- {修正1}
- {修正2}

## テスト
- [ ] 手動テスト完了
- [ ] 自動テスト追加（必要に応じて）

## 参照
- BUG-FIX-{timestamp}

---
🤖 Generated with Claude Code"
```

### Phase 5: レビューとマージ（Manus）

1. **PRをレビュー**
   - 修正が適切か確認
   - 副作用がないか確認
   - テストが追加されているか確認

2. **マージ**
   ```bash
   gh pr merge {PR番号} --merge
   ```

3. **タスクファイルを更新**
   - 全てのチェックボックスを完了にする

4. **E2Eテストを再実行**
   - 修正が正しく機能することを確認

---

## 複数バグが発見された場合

E2Eテスト中に複数のバグが発見された場合：

1. **全てのバグを先に記録**
   - 各バグに対してBUG-FIXタスクファイルを作成
   - 優先度を設定

2. **依存関係を分析**
   - バグAを修正しないとバグBが確認できない場合、Aを先に修正

3. **並行修正が可能な場合**
   - 異なるトラックのバグは並行して修正可能
   - 同じファイルに関連するバグは順次修正

---

## ブランチ命名規則

| パターン | 用途 | 例 |
|:---|:---|:---|
| `bugfix/{track}/bug-fix-*` | E2Eテストで発見されたバグ修正 | `bugfix/mobile/bug-fix-websocket-payload` |
| `hotfix/*` | 本番環境の緊急修正 | `hotfix/critical-auth-issue` |

---

## アンチパターン（避けるべき行動）

### ❌ Manusが直接修正する

```
# 悪い例
Manus: "簡単なバグなので直接修正しました"
→ ワークフローの原則に違反
```

### ❌ タスクファイルなしで修正を依頼

```
# 悪い例
Manus: "WebSocketのペイロードがおかしいので直してください"
→ 記録が残らず、再発時に追跡困難
```

### ❌ 複数のバグを1つのPRで修正

```
# 悪い例
Claude Code: "3つのバグを全て1つのPRで修正しました"
→ レビューが困難、ロールバックが複雑
```

---

## チェックリスト

E2Eテストフェーズで使用するチェックリスト：

- [ ] バグ発見時、BUG-FIXタスクファイルを作成したか
- [ ] 適切なClaude Codeインスタンスに委任したか
- [ ] Manusは直接コードを修正していないか
- [ ] PRがレビューされてからマージされたか
- [ ] 修正後、E2Eテストを再実行したか

---

## 参考資料

- [並行開発ガイド](./parallel-development-guide.md)
- [CLAUDE.md - プロジェクト設定](../CLAUDE.md)
