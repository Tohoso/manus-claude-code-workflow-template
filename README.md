# Manus × Claude Code 連携開発ワークフローテンプレート

## 概要

このリポジトリは、**Manus** をオーケストレーター、**Claude Code** を実装担当とする、高度に自動化された並行開発ワークフローのテンプレートです。実際のプロジェクト「Remote Cursor」開発で確立されたベストプラクティスを凝縮しています。

### コアコンセプト

| 役割 | 担当エージェント | 主な責務 |
|:---|:---|:---|
| 🧠 **オーケストレーター** | **Manus** | 要件定義、アーキテクチャ設計、タスクの分解・割り当て、コードレビュー、コンフリクト解決、プロジェクト全体の進捗管理 |
| 💻 **実装担当** | **Claude Code** | 個別タスクの実装、ユニットテスト作成、`progress.md` の更新、自動PR作成 |

## ワークフローアーキテクチャ

このワークフローは、Git Worktree を活用して物理的に分離された環境で複数のClaude Codeインスタンスを並行稼働させ、Manusが中央からタスクを割り当て、成果物をレビュー・統合することで、開発速度を最大化します。

```mermaid
graph TD
    subgraph GitHub Repository
        direction LR
        main[main]
        develop[develop]
        feature_a[feature/track-a/...]
        feature_b[feature/track-b/...]
        main --> develop
        develop --> feature_a
        develop --> feature_b
    end

    subgraph Local Machine
        direction TB
        subgraph Manus [🧠 Manus (Orchestrator)]
            direction LR
            task_creation[タスク作成<br>(tasks/TASK-XXX.md)]
            pr_review[PRレビュー & マージ]
            conflict_resolution[コンフリクト解決]
        end

        subgraph Claude Instances
            direction LR
            subgraph Worktree A [project-a/]
                claude1[💻 Claude-1]
            end
            subgraph Worktree B [project-b/]
                claude2[💻 Claude-2]
            end
        end
    end

    Manus -- "1. Assigns Tasks" --> GitHub
    GitHub -- "2. Pull" --> Claude Instances
    claude1 -- "3. Implements & Pushes" --> feature_a
    claude2 -- "3. Implements & Pushes" --> feature_b
    feature_a -- "4. Creates PR" --> develop
    feature_b -- "4. Creates PR" --> develop
    develop -- "5. Review Request" --> Manus
```

## テンプレートの主要コンポーネント

| ファイル/ディレクトリ | 目的 |
|:---|:---|
| `CLAUDE.md` | **プロジェクトの憲法**。Claude Codeが最初に読むファイルで、役割、ルール、ワークフロー全体を定義します。 |
| `.claude/skills/` | **自動化の心臓部**。`auto-pr-creator`や`autonomous-worktree-manager`など、ワークフローを自動化するスクリプトを格納します。 |
| `.claude/agents/` | **役割定義**。`main-agent`, `plan-agent`など、Claude Codeの内部的な役割分担を定義します。 |
| `progress.md` | **単一の真実**。全タスクのステータスを管理する共有ファイル。コンフリクトが発生した場合はManusが解決します。 |
| `tasks/` | **指示書**。Manusが作成するタスク定義ファイルを格納します。 |

## セットアップ手順

1.  **リポジトリの作成**: このテンプレートを基に、新しいGitHubリポジトリを作成します。

2.  **ファイルのコピー**: このテンプレート内のすべてのファイル（`.claude/`, `CLAUDE.md`, `progress.md`など）を新しいリポジトリにコピーしてプッシュします。

3.  **最初のタスク作成**: Manusが最初のタスク（例: `tasks/TASK-001-init-project.md`）を作成し、`develop`ブランチにプッシュします。

4.  **Claude Codeの起動**: 最初のClaude Codeインスタンスを起動し、以下のプロンプトで開始します。

    ```
    このプロジェクトの開発を開始してください。
    まず CLAUDE.md を読んでプロジェクトの概要とルールを理解してください。
    次に、autonomous-worktree-manager スキルを使い、
    tasks/ ディレクトリにある最初のタスクの担当トラック用の作業環境をセットアップしてください。
    ```

## 並行開発の運用フロー

1.  **タスク割り当て**: Manusが複数のトラック（例: `mobile-app`, `pc-server`）のタスクファイルを`tasks/`に作成し、`develop`ブランチにプッシュします。

2.  **自律的環境構築**: 各Claude Codeインスタンスは、自分に割り当てられた未着手のタスクを検知すると、`autonomous-worktree-manager`スキルを使い、担当トラック用のGit Worktreeを自動的に作成します。

3.  **実装**: 各Claude Codeは、分離されたWorktree内で実装を行い、完了したら`progress.md`を更新して`feature/...`ブランチにプッシュします。

4.  **自動PR作成**: `auto-pr-creator`スキルが発火し、`develop`ブランチへのPull Requestが自動的に作成されます。

5.  **レビューとマージ**: Manusは通知を受け、GitHub上でPRをレビューします。コンフリクトがある場合は手動で解決し、`develop`ブランチにマージします。

このサイクルを繰り返すことで、複数の開発トラックが効率的に並行して進みます。
