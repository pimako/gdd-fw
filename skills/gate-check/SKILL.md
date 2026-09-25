---
name: gate-check
description: Gate判定の本体。TEST.mdの機械的なテスト結果を根拠にSTORY.mdの各Scenarioを判定し、複数のペルソナ(職能ロール)による並列レビュー+ループでDESIGN.mdとの整合性・品質を検証、product-ownerペルソナで最終チェックしてCHECK.mdにまとめる。ユーザーが「Gate判定して」「チェックして」「/gate-check」と言った時、またはgate-runから呼ばれた時に使う。persona-agentサブエージェントを複数回呼び出す。
---

# gate-check — Gate判定(着手前に決めた確認条件を満たしたかの確認)

これが GDD の核心。`gate-story` で**着手前に定義した確認条件(Gherkin Scenario)**が、実際に満たされているかを判定する。単一の汎用レビュアーではなく、**職能ロール(ペルソナ)ごとに独立したサブエージェント**を並列に走らせて指摘を統合する(同一モデルが自分の出力を甘く評価するバイアスを軽減するため)。ラウンドは最大2回に制限し、それ以上は人間に判断を委ねる(多数ラウンドの討論はバイアスを増幅するという研究知見のため)。

## 前提チェック

`.specs/stories/<slug>/TEST.md` が存在しない場合は、先に `gate-test` を完了させるようユーザーに促す。

## 手順

1. `TEST.md`(機械的なテスト結果)を根拠に、`STORY.md`の各Scenarioが満たされているかを確認する(推測で合格にしない)。
2. `.specs/PERSONAS.md` を読む。
   - 存在しない場合: `tech-lead`, `security-engineer`, `qa-engineer` の3つを暫定ロスターとして使い、CHECK.mdに「`.specs/PERSONAS.md` が未設定のため暫定ロスターを使用。`/gate-team` の実行を推奨」と注記する。
   - 存在する場合: 有効なペルソナのうち `phases` に `review` を含むもの(プロジェクトのカスタムペルソナ含む)を対象にする。
3. **Round 1**: 対象ペルソナの数だけ `persona-agent` サブエージェントを、**1メッセージ内でまとめて並列起動**する。各呼び出しにはSTORY.md・DESIGN.md・TEST.md・実装差分を渡す。レビュー観点には「DESIGN.mdとの整合性(逸脱していないか)」を含める。各ペルソナは他のペルソナの意見を見ずに独立して指摘を返す。
4. 全指摘を `.specs/stories/<slug>/CHECK.md` にペルソナ別セクションで記録する。
5. **メタ統合**(個々の指摘を再討論させるのではなく俯瞰して整理する): 同じ箇所を指す指摘をまとめ、重大度順に並べ、ペルソナ間で矛盾する指摘があれば「要判断」として明示する。
6. critical/majorの指摘が残る場合: ユーザーに要約して報告し、`gate-build`(実装の問題)・`gate-test`(テスト自体の見直し)・`gate-design`(設計判断自体の問題)のいずれかに差し戻す。対応後、**Round 2**として手順3〜5を再実行する。
7. **Round 2を超えてもcritical/majorが残る場合はループを打ち切り**、CHECK.mdに「人間判断待ち」として残課題を明記し、ユーザーに判断を委ねる(Round 3以降は行わない)。
8. `.specs/PERSONAS.md` に `product-owner` が有効な場合、`persona-agent` を1回(単発)起動し、STORY.mdと判定結果を渡して「これは本当にユーザーが欲しかったものか」の最終チェックを行う。
9. `CHECK.md` に最終判定を記録する。全Scenario PASS・重大な指摘なしなら、ストーリーのステータスを `done` にし `BACKLOG.md` を更新する。
10. 次は所属イテレーションの残りストーリー次第で `gate-iteration`(全ストーリー完了時)に進むことを案内する。

## CHECK.md フォーマット

```markdown
# Check: <ストーリータイトル>

## Scenario判定(TEST.mdを根拠)
- [x] <Scenario名>: PASS
- [ ] <Scenario名>: FAIL(理由: ...)

## ロスター
<使用したペルソナ一覧、暫定ロスターの場合はその旨>

## Round 1
### tech-lead
- [major] ...(該当: ...)

### security-engineer
- (指摘なし)

## 統合サマリ(Round 1)
- ...

## Round 2(実施した場合のみ)
...

## product-ownerによる最終チェック
- ...

## Gate判定
PASS / FAIL(人間判断待ち。理由: ...)
```
