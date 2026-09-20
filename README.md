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
| レビュー | `spec-review` | `review.md` | `persona-agent`(複数ペルソナ・並列) |
| 受け入れ検証 | `spec-verify` | `verify.md` | `persona-agent`(product-owner、単発) |
| 振り返り | `spec-retro` | `retro.md` | - |

まとめて実行したい場合は `spec-run` を使うと、上記フェーズを順に実行し、仕様確定・設計確定・タスク確定の3ゲートでユーザーの承認を取りながら進めます。現在の進捗は `spec-status` でいつでも確認できます。

## 補助スキル

パイプラインのフェーズに固定されない、独立して呼び出せるスキルです。

| Skill | 役割 |
|---|---|
| `spec-team` | プロジェクト単位のペルソナロスター(`.specs/PERSONAS.md`)を設定・更新する。`spec-review`/`spec-verify`/`spec-discuss` の土台。機能ごとではなく一度設定すればよい |
| `spec-research` | Web上の先行事例・ベストプラクティス・規制/標準を調査し `research.md` を作る。`spec-spec`/`spec-design` の判断材料 |
| `spec-discuss` | 任意のトピックについて、関連するペルソナを選んで討議させ、合意点・対立点をまとめる |

## なぜ複数ペルソナでレビューするのか

単一の汎用レビュアーが自分(同じモデル)の出力を評価すると、self-enhancement bias(自分の出力を甘く評価する傾向)などの相関したバイアスが生じやすいことが知られています。このプラグインでは、`tech-lead` `security-engineer` `qa-engineer` のような**職能ロール(ペルソナ)ごとに独立したサブエージェントを並列に走らせ**、指摘を統合する方式でこれを緩和します。

ただし、ラウンドを重ねる討論はバイアスを増幅する可能性があるという研究結果があるため、レビューループは**最大2ラウンド**までとし、それでも解決しない場合は必ず人間に判断を委ねます(`spec-review`のSKILL.md参照)。

ペルソナは `personas/` に役割(短い名詞句)・ゴール(単一・100語以内)・バックストーリー(3〜5文の具体的な文脈)という統一フォーマットで定義されています。プロジェクトごとに `.specs/personas/` へカスタムペルソナ(独自ドメイン知識を持つ `domain-specialist` の上書きを含む)を追加できます。

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
2. (推奨・任意)`/spec-team` でこのプロジェクトのペルソナロスターを設定する。
3. `/spec-new` で新機能のパイプラインを初期化する(または最初から `/spec-run` で通しで開始してもよい)。
4. 各フェーズを個別に進めたい場合は `/spec-spec` `/spec-design` `/spec-tasks` `/spec-implement` `/spec-test` `/spec-review` `/spec-verify` `/spec-retro` を順に実行する。
5. 進捗を確認したいときは `/spec-status`。関連情報を調べたいときは `/spec-research`。任意のトピックについてペルソナに討議させたいときは `/spec-discuss`。

## ディレクトリ構成

```
spec-driven-dev/
  .claude-plugin/plugin.json
  personas/
    product-owner.md
    domain-specialist.md      # テンプレート。プロジェクトごとに .specs/personas/ で上書き
    end-user-advocate.md
    legal-compliance.md
    tech-lead.md
    security-engineer.md
    performance-analyst.md
    sre-ops.md
    data-engineer.md
    ml-engineer.md
    embedded-systems-engineer.md
    qa-engineer.md
    release-manager.md
    ux-reviewer.md
    accessibility-specialist.md
    technical-writer.md
    localization-specialist.md
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
    spec-team/SKILL.md
    spec-research/SKILL.md
    spec-discuss/SKILL.md
  agents/
    spec-architect.md
    spec-planner.md
    spec-tester.md
    persona-agent.md
  README.md
```

## 成果物ディレクトリ(対象プロジェクト側)

```
.specs/
  PERSONAS.md              # プロジェクト単位のペルソナロスター(spec-teamが作成)
  personas/                # プロジェクト固有・カスタムのペルソナ定義
    domain-specialist.md
    <custom>.md
  research/                # 機能に紐づかない調査結果(spec-research)
    <topic-slug>.md
  discussions/              # 機能に紐づかない討議結果(spec-discuss)
    <topic-slug>.md
  <feature-slug>/
    STATE.md      # 現在フェーズ・進捗チェックリスト
    spec.md       # 要件・受け入れ基準
    design.md     # アーキテクチャ・技術判断
    tasks.md      # タスク一覧(依存関係付き)
    test.md       # テスト方針・結果
    review.md     # 複数ペルソナのレビュー指摘・統合サマリ
    verify.md     # 受け入れ基準との最終突合 + product-ownerチェック
    retro.md      # 振り返り
    research.md   # この機能に関する調査結果(spec-research、任意)
    discussions/  # この機能に関する討議結果(spec-discuss、任意)
      <topic-slug>.md
```
