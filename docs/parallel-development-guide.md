# 並行開発ガイド

## 概要

このガイドでは、Manus × Claude Code連携ワークフローにおける並行開発の運用方法を説明します。Git Worktreeを活用することで、複数のClaude Codeインスタンスが同時に異なるトラックで作業できます。

---

## Git Worktreeとは

Git Worktreeは、1つのリポジトリから複数の作業ディレクトリを作成するGitの機能です。各ディレクトリは独立したブランチで作業でき、互いに干渉しません。

### 従来の方法との比較

| 方法 | メリット | デメリット |
|:---|:---|:---|
| **ブランチ切り替え** | シンプル | 同時に1ブランチのみ、混線リスク |
| **複数クローン** | 完全分離 | ディスク容量2倍、同期が面倒 |
| **Git Worktree** | 分離 + 効率的 | 学習コストあり |

---

## セットアップ手順

### 1. メインリポジトリのクローン

```bash
git clone https://github.com/your-org/your-project.git
cd your-project
```

### 2. developブランチの準備

```bash
git checkout develop
git pull origin develop
```

### 3. トラック用Worktreeの作成

```bash
# Mobile Appトラック用
git worktree add ../your-project-mobile develop

# PC Serverトラック用
git worktree add ../your-project-server develop

# Docsトラック用
git worktree add ../your-project-docs develop
```

### 4. ディレクトリ構造の確認

```
~/projects/
├── your-project/           # メイン（または Track A）
├── your-project-mobile/    # Mobile App トラック
├── your-project-server/    # PC Server トラック
└── your-project-docs/      # Docs トラック
```

---

## Cursorでの運用

### 複数ウィンドウの起動

各トラックは**別々のCursorウィンドウ**で開きます。

1. **ウィンドウ1**: `your-project-mobile/` を開く → Claude-1
2. **ウィンドウ2**: `your-project-server/` を開く → Claude-2
3. **ウィンドウ3**: `your-project-docs/` を開く → Claude-3

### 各Claude Codeへの初期指示

各ウィンドウでClaude Codeを起動し、以下を伝えます：

```
あなたは「Claude-{N}」として、{Track Name} トラックを担当します。

1. CLAUDE.md を読んでプロジェクトのルールを理解してください
2. progress.md で現在の状況を確認してください
3. tasks/ ディレクトリから自分のトラックのタスクを見つけて実行してください

作業は feature/{track}/{task-id}-* ブランチで行ってください。
```

---

## ブランチ戦略

### ブランチ命名規則

| パターン | 用途 | 例 |
|:---|:---|:---|
| `main` | 本番リリース | - |
| `develop` | 統合ブランチ | - |
| `feature/{track}/{task-id}-*` | 機能開発 | `feature/mobile/task-004-websocket` |
| `manus/*` | Manusによる変更 | `manus/add-task-005` |

### マージフロー

```
feature/mobile/task-004-* ──┐
                            ├──> develop ──> main
feature/server/task-005-* ──┘
```

---

## コンフリクト解決

### progress.mdのコンフリクト

`progress.md`は全トラックで共有されるため、コンフリクトが発生しやすいファイルです。

**解決方針**: Manusがオーケストレーターとして解決します。

```bash
# Manusが実行
git checkout develop
git pull origin develop
git merge origin/feature/mobile/task-004-*
# コンフリクト発生時
git checkout --theirs progress.md  # または手動マージ
git add progress.md
git commit -m "chore: resolve progress.md conflict"
git push origin develop
```

### その他のコンフリクト

- **同一ファイルの変更**: タスク設計時に回避（異なるファイルを担当させる）
- **依存関係のあるタスク**: 依存タスクが完了するまでブロック

---

## トラブルシューティング

### Worktreeが作成できない

```bash
# エラー: 'develop' is already checked out at '...'
git worktree add ../project-mobile -b mobile-work develop
```

### Worktreeの削除

```bash
git worktree remove ../your-project-mobile
```

### Worktree一覧の確認

```bash
git worktree list
```

---

## ベストプラクティス

1. **トラックの独立性を保つ**: 各トラックは異なるディレクトリ/モジュールを担当
2. **頻繁にプル**: 作業開始前に必ず `git pull origin develop`
3. **小さなPR**: 大きな変更は分割してコンフリクトリスクを低減
4. **progress.mdは最後に更新**: コミット直前に更新してコンフリクトを最小化
5. **Manusに任せる**: コンフリクト解決はManusの責務

---

## 参考資料

- [Git Worktree 公式ドキュメント](https://git-scm.com/docs/git-worktree)
- [GitHub CLI ドキュメント](https://cli.github.com/manual/)
