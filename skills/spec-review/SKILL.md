---
name: spec-review
description: 実装がspec.md・design.mdと整合しているか、スコープ逸脱や品質問題がないかを、複数のペルソナ(職能ロール)による並列レビュー+ループでreview.mdにまとめる仕様駆動開発のレビューフェーズ。ユーザーが「レビューして」「/spec-review」と言った時、またはspec-runから呼ばれた時に使う。persona-agentサブエージェントを複数回呼び出す。
---

# spec-review — レビューフェーズ(複数ペルソナ・レビューループ)

単一の汎用レビュアーではなく、**職能ロール(ペルソナ)ごとに独立したサブエージェント**を並列に走らせ、その指摘を統合する。同一モデルが自分の出力を甘く評価する(self-enhancement bias)リスクを、単一レビュアーより軽減するための設計。ただし多数ラウンドの討論はバイアスを増幅する研究結果があるため、**ラウンドは最大2回**に制限し、それ以上は人間に判断を委ねる。

## 前提チェック

`.specs/<slug>/tasks.md` の実装が実質的に未着手の場合は、先に `spec-implement` を進めるようユーザーに促す。

## 手順

1. `.specs/<slug>/spec.md`, `design.md`, `tasks.md`、実装差分(git diff等)を集める。
2. `.specs/PERSONAS.md` を読む。
   - 存在しない場合: `tech-lead`, `security-engineer`, `qa-engineer` の3つを暫定ロスターとして使い、review.mdに「`.specs/PERSONAS.md` が未設定のため暫定ロスターを使用。`/spec-team` の実行を推奨」と注記する。
   - 存在する場合: 有効なペルソナのうち `phases` に `review` を含むもの(プロジェクトのカスタムペルソナ含む)を対象にする。各ペルソナの定義は `personas/<name>.md`(プロジェクト側に `.specs/personas/<name>.md` があればそちらを優先)から読む。
3. **Round 1**: 対象ペルソナの数だけ `persona-agent` サブエージェントを、**1メッセージ内でまとめて並列起動**する(Agent tool)。各呼び出しには、対象ペルソナのMarkdown全文、spec.md/design.md/実装差分を渡す。各ペルソナは他のペルソナの意見を見ずに独立して指摘を返す(レビュー用途の出力形式に従う)。
4. 全指摘を `.specs/<slug>/review.md` にペルソナ別セクションで記録する。
5. **メタ統合**(個々の指摘を再討論させるのではなく、俯瞰して整理する):
   - 同じ箇所を指す指摘をまとめる
   - 重大度順に並べる
   - ペルソナ間で矛盾する指摘(例: performance-analystはキャッシュを求め、tech-leadは複雑化を懸念)があれば「要判断」として明示する
   - 「統合サマリ」として review.md に書く
6. critical/majorの指摘が残る場合:
   - ユーザーに要約して報告し、`spec-implement` に戻って対応する
   - 対応後、**Round 2**として手順3〜5を再実行する(review.mdにRound 2として追記)
7. **Round 2を超えてもcritical/majorが残る場合はループを打ち切り**、review.mdに「人間判断待ち」として残課題を明記し、ユーザーに判断を委ねる(Round 3以降は行わない)。
8. `.specs/<slug>/STATE.md` を更新(`current_phase: review`、進捗チェック、`updated_at`)。
9. 次は `spec-verify` に進むことを案内する。

## review.md フォーマット

```markdown
# Review: <機能名>

## ロスター
<使用したペルソナ一覧、暫定ロスターの場合はその旨>

## Round 1
### tech-lead
- [major] ...(該当: ...)

### security-engineer
- (指摘なし)

### qa-engineer
- [minor] ...(該当: ...)

## 統合サマリ(Round 1)
- ...(重複統合・重大度順・矛盾する指摘があれば明示)

## Round 2(実施した場合のみ)
...

## 最終ステータス
解決済み / 人間判断待ち(理由)
```
