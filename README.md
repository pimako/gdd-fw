# gate-driven-dev (gdd-fw)

Claude Code 用の「Gate-Driven Development(GDD)」プラグイン。GDDの核心は、**着手前に完了要件(Gate条件)を決めておき、完了時にそれを満たしたかを確認する**という原則を、パイプラインのあらゆる遷移に適用すること。アジャイルサムライの3階層構造(プロジェクト/イテレーション/ストーリー)に沿って設計されている。

## 3階層構造

```
① プロジェクト(一度きり)
   gate-init(インセプション) → gate-team(ペルソナロスター)

② イテレーション(繰り返す。ストーリー件数/ゴールで区切る)
   gate-iteration(計画) → ... → gate-iteration(ふりかえり)

③ ストーリー(軽量。1機能=1カード)
   gate-story(確認条件をGherkinで定義)
     → gate-design(How: 技術方針)
     → gate-plan(タスク分解)
     → gate-build(実装+単体テスト)
     → gate-test(統合/システム/受け入れテスト)
     → gate-check(Gate判定: 確認条件を満たしたか)
```

`gate-run` を使うと①〜③を通しで実行できる(仕様確定・設計確定・タスク確定でユーザー承認を取りながら進む)。現在の状況は `gate-status` でいつでも確認できる。

## なぜ「Gate」なのか

タスクは「小さければよい」のではなく「独立して価値をデリバリーできるか」が本質。ストーリーは技術的都合と完全に疎結合であるべきで、**確認条件はGherkin(Given-When-Then)で、ユーザーから観測可能な振る舞いの言葉だけで書く**(実装技術には言及しない)。技術的な判断はすべて`gate-design`に属し、これは規模に関わらずすべてのストーリーで必ず通過する(大きな機能のための特別な逃げ道は用意しない)。

`gate-build`の実装中に計画(TASKS.md/DESIGN.md)が破綻していると分かった場合は、その場しのぎにせず、ユーザーの同意を得たうえで該当フェーズに戻って改訂する。

テストはISTQBのレベル分けに従い、`gate-build`はコンポーネント/単体テストまで、`gate-test`が統合・システム・受け入れテストを担当する(Gherkin Scenarioはここで実際のテストとして実行される)。`gate-check`は`gate-test`の機械的な結果を根拠にしつつ、複数のペルソナ(職能ロール)による並列レビュー+最大2ラウンドのループで最終判定する。

## なぜ複数ペルソナでレビューするのか

単一の汎用レビュアーが自分(同じモデル)の出力を評価すると、self-enhancement bias(自分の出力を甘く評価する傾向)が生じやすい。`gate-check`/`gate-design`では、`tech-lead` `security-engineer` `qa-engineer` のような**職能ロール(ペルソナ)ごとに独立したサブエージェントを並列に走らせ**、指摘を統合する。ラウンドを重ねる討論はバイアスを増幅し得るため、ループは**最大2ラウンド**までとし、それでも解決しなければ必ず人間に判断を委ねる。

ペルソナは `personas/` に役割(短い名詞句)・ゴール(単一・100語以内)・バックストーリー(3〜5文の具体的な文脈)という統一フォーマットで定義されている。プロジェクトごとに `.specs/personas/` へカスタムペルソナを追加できる。

## 補助スキル

パイプラインのフェーズに固定されない、独立して呼び出せるスキル。

| Skill | 役割 |
|---|---|
| `gate-status` | バックログ・イテレーション・各ストーリーの状況を一覧表示 |
| `gate-research` | Web上の先行事例・ベストプラクティス・規制/標準を調査 |
| `gate-discuss` | 任意のトピックについて、関連するペルソナを選んで討議させる |

## インストール

### ローカルで試す(開発中のプロジェクトから)

対象プロジェクトの `.claude/settings.json`(または `settings.local.json`)にローカルパスでプラグインを追加する。

```json
{
  "plugins": {
    "gate-driven-dev": {
      "path": "C:/Users/USER/develop/spec-driven-dev"
    }
  }
}
```

### marketplace / git 経由で配布する場合

このディレクトリを git リポジトリとして公開し、Claude Code のプラグイン marketplace の仕組みでインストールできるようにする(`plugin.json` は準備済み)。

## 使い方

1. 対象プロジェクトのディレクトリで Claude Code を開く。
2. `/gate-init` でプロジェクトを立ち上げる(インセプションデッキ)。
3. (推奨)`/gate-team` でこのプロジェクトのペルソナロスターを設定する。
4. `/gate-story` で最初のストーリーを書く、または `/gate-run` で通しで進める。
5. 各フェーズを個別に進めたい場合は `/gate-design` `/gate-plan` `/gate-build` `/gate-test` `/gate-check` を順に実行する。
6. イテレーションの区切りで `/gate-iteration`(計画/ふりかえりは状態に応じて自動判定)。
7. 進捗を確認したいときは `/gate-status`。関連情報を調べたいときは `/gate-research`。任意のトピックについてペルソナに討議させたいときは `/gate-discuss`。

## ディレクトリ構成

```
spec-driven-dev/                      # 物理フォルダ名(プラグイン名は plugin.json の name)
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
    gate-init/SKILL.md
    gate-team/SKILL.md
    gate-story/SKILL.md
    gate-design/SKILL.md
    gate-plan/SKILL.md
    gate-build/SKILL.md
    gate-test/SKILL.md
    gate-check/SKILL.md
    gate-iteration/SKILL.md
    gate-run/SKILL.md
    gate-status/SKILL.md
    gate-research/SKILL.md
    gate-discuss/SKILL.md
  agents/
    gate-architect.md    # gate-designで技術方針を起草
    gate-planner.md       # gate-planでタスクに分解
    gate-tester.md          # gate-testで統合/システム/受け入れテストを実施
    persona-agent.md          # 指定ペルソナになりきる汎用ロールプレイヤー
  README.md
```

## 成果物ディレクトリ(対象プロジェクト側)

```
.specs/
  INCEPTION.md              # gate-init: インセプションデッキの回答
  PERSONAS.md               # gate-team: プロジェクト単位のペルソナロスター
  personas/                 # プロジェクト固有・カスタムのペルソナ定義
    domain-specialist.md
    <custom>.md
  BACKLOG.md                # マスターストーリーリスト(全ストーリー、優先度、見積り、ステータス、所属イテレーション)
  iterations/
    <n>/
      PLAN.md                # 今回選んだストーリー群・ゴール・開始日
      RETRO.md                # ふりかえり(Keep/Problem/Try)
  stories/
    <story-slug>/
      STORY.md                  # Feature(As a/I want/so that)+ Scenario群(Given/When/Then = 確認条件/Gate)
      DESIGN.md                   # 技術方針・主要な判断(ストーリーのサイズに応じて軽重を調整)
      TASKS.md                      # 軽量タスク分解
      TEST.md                        # 統合/システム/受け入れテストの結果
      CHECK.md                        # Gate判定: Scenario判定+設計整合+ペルソナレビュー+最終判定(PASS/FAIL)
      research.md                       # 任意: このストーリーの調査結果(gate-research)
      discussions/                        # 任意: このストーリーに関する討議結果(gate-discuss)
        <topic-slug>.md
  research/
    <topic-slug>.md          # ストーリーに紐づかない調査結果
  discussions/
    <topic-slug>.md          # ストーリーに紐づかない討議結果
```
