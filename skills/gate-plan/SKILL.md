---
name: gate-plan
description: DESIGN.mdの方針を踏まえ、ストーリーを数時間〜1日粒度のタスクに分解しTASKS.mdを作る。ユーザーが「タスクに分解したい」「/gate-plan」と言った時、またはgate-runから呼ばれた時に使う。gate-plannerサブエージェントに委任する。
---

# gate-plan — タスク分解

## 前提チェック

`.specs/stories/<slug>/DESIGN.md` が存在しない場合は、先に `gate-design` を完了させるようユーザーに促す。

## 手順

1. `STORY.md` と `DESIGN.md` を読む。
2. **gate-planner** サブエージェント(Agent tool)に両ファイルを渡し、DESIGN.mdの方針に沿ったタスクへの分解を依頼する。各タスクは:
   - 数時間〜1日で完了できる粒度
   - 依存関係が明確
   - 対応するDESIGN.mdの判断・STORY.mdのScenarioが分かる
   - コンポーネント/単体テストの作成を含む(統合・受け入れテストはgate-testの範囲)
3. `.specs/stories/<slug>/TASKS.md` を書く。
4. タスク一覧をユーザーに提示し、抜け漏れや順序に問題がないか確認する。
5. ステータスを更新し、次は `gate-build` に進むことを案内する。

## TASKS.md フォーマット

```markdown
# Tasks: <ストーリータイトル>

- [ ] T1. <タスク内容>(関連: DESIGN.md#<節>, <Scenario名> / 依存: なし)
- [ ] T2. <タスク内容>(依存: T1)
```
