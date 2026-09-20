---
name: spec-verify
description: spec.mdの受け入れ基準を1つずつ最終突合し合否をverify.mdに記録する仕様駆動開発の受け入れ検証フェーズ。ユーザーが「受け入れ確認して」「完成か確認して」「/spec-verify」と言った時、またはspec-runから呼ばれた時に使う。product-ownerペルソナによる最終チェックも行う。
---

# spec-verify — 受け入れ検証フェーズ

## 前提チェック

`.specs/<slug>/review.md` が存在しない場合は、先に `spec-review` を完了させるようユーザーに促す。

## 手順

1. `.specs/<slug>/spec.md` の受け入れ基準を1件ずつ取り出す。
2. 各基準について、実装・test.md・review.md を根拠に満たしているか判定する。実機・実データでの確認が必要な場合はユーザーに確認を依頼する(推測で合格にしない)。
3. `.specs/PERSONAS.md` に `product-owner` が有効な場合、`persona-agent` を1回(ループなし・単発)起動し、spec.md・受け入れ基準の判定結果を渡して「これは本当にユーザーが欲しかったものか」という視点での最終チェックを行う。無効/未設定の場合はスキップしてよい。
4. `.specs/<slug>/verify.md` を以下の構成で書く。

   ```markdown
   # Verify: <機能名>

   ## 受け入れ基準の判定
   - [x] 基準1: ...(根拠: ...)
   - [ ] 基準2: 未達(理由: ...)

   ## product-ownerによる最終チェック(実施した場合)
   - ...

   ## 総合判定
   合格 / 条件付き合格 / 不合格

   ## 残課題(あれば)
   - ...
   ```

5. 未達の基準、またはproduct-ownerチェックで重大な懸念があれば、ユーザーに報告し `spec-implement` または `spec-design` に戻るか判断を仰ぐ。
6. `.specs/<slug>/STATE.md` を更新(`current_phase: verify`、進捗チェック、`updated_at`)。
7. 全基準合格であれば、次は `spec-retro` に進むことを案内する。
