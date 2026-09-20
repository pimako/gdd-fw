---
name: spec-review
description: 実装がspec.md・design.mdと整合しているか、スコープ逸脱や品質問題がないかをレビューしreview.mdにまとめる仕様駆動開発のレビューフェーズ。ユーザーが「レビューして」「/spec-review」と言った時、またはspec-runから呼ばれた時に使う。spec-reviewerサブエージェントに委任する。
---

# spec-review — レビューフェーズ

## 前提チェック

`.specs/<slug>/tasks.md` の実装が実質的に未着手の場合は、先に `spec-implement` を進めるようユーザーに促す。

## 手順

1. `.specs/<slug>/spec.md`, `design.md`, `tasks.md`、および実装差分(git diff等)を集める。
2. **spec-reviewer** サブエージェント(Agent tool)にこれらを渡し、以下の観点でレビューを依頼する:
   - spec.md の受け入れ基準を満たしているか
   - design.md の方針から逸脱していないか(逸脱している場合は意図的か)
   - 明らかなバグ・スコープ外の変更・不要な複雑化がないか
3. 指摘事項を重大度付きで整理し、`.specs/<slug>/review.md` を以下の構成で書く。

   ```markdown
   # Review: <機能名>

   ## 指摘事項
   - [重大度] <指摘内容>(該当ファイル:行)
     - 対応: 未対応 / 対応済み / 対応不要(理由)

   ## 総評
   ...
   ```

4. 重大な指摘があれば、ユーザーに確認のうえ `spec-implement` に戻って対応する。
5. `.specs/<slug>/STATE.md` を更新(`current_phase: review`、進捗チェック、`updated_at`)。
6. 次は `spec-verify` に進むことを案内する。
