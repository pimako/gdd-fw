---
name: spec-retro
description: パイプライン全体を振り返り学び・落とし穴・次回への申し送りをretro.mdに記録する仕様駆動開発の振り返りフェーズ。ユーザーが「振り返りたい」「/spec-retro」と言った時、またはspec-runから呼ばれた時に使う。パイプラインの最終フェーズ。
---

# spec-retro — 振り返りフェーズ

## 前提チェック

`.specs/<slug>/verify.md` が存在しない場合は、先に `spec-verify` を完了させるようユーザーに促す。

## 手順

1. `.specs/<slug>/` 配下の全ファイル(spec/design/tasks/test/review/verify)にざっと目を通す。
2. 以下を振り返る:
   - 想定より時間がかかった/手戻りが発生した箇所とその原因
   - 設計判断のうち、実際には合わなかったもの
   - 次に同種の機能を作る際に活かせる知見・注意点
3. `.specs/<slug>/retro.md` を以下の構成で書く。

   ```markdown
   # Retro: <機能名>

   ## うまくいったこと
   - ...

   ## 手戻り・想定外だったこと
   - ...

   ## 次回への申し送り
   - ...
   ```

4. `.specs/<slug>/STATE.md` を更新(`current_phase: done`、進捗チェックをすべて完了、`updated_at`)。
5. パイプライン完了をユーザーに伝える。
