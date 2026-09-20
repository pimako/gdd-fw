---
name: spec-new
description: 仕様駆動開発パイプラインを新しい機能・変更について開始する。.specs/<slug>/ ディレクトリとSTATE.mdを初期化する。ユーザーが「新機能の仕様駆動開発を始めたい」「specパイプラインを初期化したい」「/spec-new」と言った時に使う。他のspec-*スキルより先に実行する起点。
---

# spec-new

仕様駆動開発パイプラインの最初のステップ。機能に対して `.specs/<slug>/` を作成し、以降の全フェーズが状態を読み書きできるようにする。

## 共通ルール(このプラグインの全スキルで共通)

- 成果物はすべて **対象プロジェクト直下**の `.specs/<feature-slug>/` に格納する(このプラグイン自身のリポジトリではなく、ユーザーが今取り組んでいるプロジェクトのルート)。
- `<feature-slug>` は kebab-case、英数字とハイフンのみ、3〜5単語程度で機能を要約したもの(例: `user-auth-jwt`)。
- 各フェーズのファイル名は固定: `spec.md`, `design.md`, `tasks.md`, `test.md`, `review.md`, `verify.md`, `retro.md`, `STATE.md`。
- `STATE.md` には `feature`(自然文の機能名)、`slug`、`current_phase`、`updated_at`(ISO日付)を記録し、フェーズ完了のたびに更新する。
- 後続フェーズは前段の成果物ファイルが存在しない場合、先にそのフェーズを完了させるようユーザーに促し、勝手に代役を書かない。

## 手順

1. ユーザーの要望から機能概要を1〜2文で要約し、そこから `<feature-slug>` を提案する。既存の `.specs/` 配下と衝突する場合は連番などで区別する。
2. `.specs/<slug>/` ディレクトリを作成する。
3. `STATE.md` を以下のフォーマットで作成する。

   ```markdown
   # STATE

   - feature: <機能の自然文名>
   - slug: <slug>
   - current_phase: new
   - updated_at: <ISO日付>

   ## フェーズ進捗
   - [ ] spec
   - [ ] design
   - [ ] tasks
   - [ ] implement
   - [ ] test
   - [ ] review
   - [ ] verify
   - [ ] retro
   ```

4. ユーザーに、次は `spec-spec` スキル(要件定義)に進むことを伝える。まとめて全フェーズを自動で進めたい場合は `spec-run` を使うよう案内する。
