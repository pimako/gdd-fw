# spec-driven-dev

Claude Code 用の「仕様駆動開発(Spec-Driven Development)」プラグイン。要件定義から振り返りまでの開発パイプラインを、Skills とサブエージェントの組み合わせで実現します。

## パイプライン

```
spec-new → spec-spec → spec-design → spec-tasks → spec-implement → spec-test → spec-review → spec-verify → spec-retro
  (init)    (要件定義)    (設計)       (タスク分解)    (実装)         (テスト)     (レビュー)     (受け入れ検証)  (振り返り)
```

各フェーズは対象プロジェクト直下の `.specs/<feature-slug>/` に成果物を Markdown で永続化します。途中から再開でき、`spec-status` で進捗を確認できます。

| フェーズ | Skill | 成果物 | 使うサブエージェント |
|---|---|---|---|
| 初期化 | `spec-new` | `STATE.md` | - |
| 要件定義 | `spec-spec` | `spec.md` | - |
| 設計 | `spec-design` | `design.md` | `spec-architect` |
| タスク分解 | `spec-tasks` | `tasks.md` | `spec-planner` |
| 実装 | `spec-implement` | コード + `tasks.md` 更新 | - |
| テスト | `spec-test` | `test.md` | `spec-tester` |
| レビュー | `spec-review` | `review.md` | `spec-reviewer` |
| 受け入れ検証 | `spec-verify` | `verify.md` | - |
| 振り返り | `spec-retro` | `retro.md` | - |

まとめて実行したい場合は `spec-run` を使うと、上記フェーズを順に実行し、仕様確定・設計確定・タスク確定の3ゲートでユーザーの承認を取りながら進めます。現在の進捗は `spec-status` でいつでも確認できます。

## インストール

### ローカルで試す(開発中のプロジェクトから)

対象プロジェクトの `.claude/settings.json`(または `settings.local.json`)にローカルパスでプラグインを追加します。

```json
{
  "plugins": {
    "spec-driven-dev": {
      "path": "C:/Users/USER/develop/spec-driven-dev"
    }
  }
}
```

### marketplace / git 経由で配布する場合

このディレクトリを git リポジトリとして公開し、Claude Code のプラグイン marketplace の仕組みでインストールできるようにします(`plugin.json` は準備済み)。

## 使い方

1. 対象プロジェクトのディレクトリで Claude Code を開く。
2. `/spec-new` で新機能のパイプラインを初期化する(または最初から `/spec-run` で通しで開始してもよい)。
3. 各フェーズを個別に進めたい場合は `/spec-spec` `/spec-design` `/spec-tasks` `/spec-implement` `/spec-test` `/spec-review` `/spec-verify` `/spec-retro` を順に実行する。
4. 進捗を確認したいときは `/spec-status`。

## ディレクトリ構成

```
spec-driven-dev/
  .claude-plugin/plugin.json
  skills/
    spec-new/SKILL.md
    spec-spec/SKILL.md
    spec-design/SKILL.md
    spec-tasks/SKILL.md
    spec-implement/SKILL.md
    spec-test/SKILL.md
    spec-review/SKILL.md
    spec-verify/SKILL.md
    spec-retro/SKILL.md
    spec-status/SKILL.md
    spec-run/SKILL.md
  agents/
    spec-architect.md
    spec-planner.md
    spec-tester.md
    spec-reviewer.md
  README.md
```

## 成果物ディレクトリ(対象プロジェクト側)

```
.specs/<feature-slug>/
  STATE.md      # 現在フェーズ・進捗チェックリスト
  spec.md       # 要件・受け入れ基準
  design.md     # アーキテクチャ・技術判断
  tasks.md      # タスク一覧(依存関係付き)
  test.md       # テスト方針・結果
  review.md     # レビュー指摘・対応状況
  verify.md     # 受け入れ基準との最終突合
  retro.md      # 振り返り
```
