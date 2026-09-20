---
name: spec-tasks
description: design.mdを実装可能な単位のタスクリストtasks.mdに分解する仕様駆動開発のタスク分解フェーズ。ユーザーが「タスクに分解したい」「/spec-tasks」と言った時、またはspec-runから呼ばれた時に使う。spec-plannerサブエージェントに委任する。
---

# spec-tasks — タスク分解フェーズ

## 前提チェック

`.specs/<slug>/design.md` が存在しない場合は、先に `spec-design` を完了させるようユーザーに促し、ここでは進めない。

## 手順

1. `.specs/<slug>/spec.md` と `design.md` を読む。
2. **spec-planner** サブエージェント(Agent tool)に両ファイルを渡し、実装可能な粒度のタスクへの分解を依頼する。各タスクは以下を満たすこと:
   - 1タスク = 概ね1回の実装セッションで完了できる大きさ
   - 依存関係が明確(先に終わらせるべきタスクが分かる)
   - 対応する受け入れ基準・設計セクションが分かる
3. `.specs/<slug>/tasks.md` を以下の構成で書く。

   ```markdown
   # Tasks: <機能名>

   - [ ] T1. <タスク内容>(関連: design.md#<節>, spec.mdの受け入れ基準n)
   - [ ] T2. <タスク内容>(依存: T1)
   - ...
   ```

4. `.specs/<slug>/STATE.md` を更新(`current_phase: tasks`、進捗チェック、`updated_at`)。
5. タスク一覧をユーザーに提示し、抜け漏れや順序に問題がないか確認する。問題なければ次は `spec-implement` に進むことを案内する。
