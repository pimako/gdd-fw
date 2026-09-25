---
name: gate-story
description: ストーリーカードを作成する。As a/I want/so that と、着手前に定義する確認条件(GherkinのScenario、Given/When/Then)をまとめ、INVESTチェックのうえBACKLOG.mdに追加する。ユーザーが「ストーリーを書きたい」「新しい機能を追加したい」「/gate-story」と言った時に使う。GDDの3階層のうち「ストーリー単位」の起点。
---

# gate-story — ストーリーカード作成

1つの機能・変更を、**技術的な都合と完全に切り離した**カードとして定義する。ここで決める確認条件(Gate)が、`gate-check` での合否判定の基準になる。

## 前提チェック

`.specs/INCEPTION.md` が無ければ、先に `gate-init` を実行するようユーザーに促す。

## 手順

1. 対話でストーリーの中身を固める(推測で埋めない):
   - **As a** <誰が> **I want** <何を> **so that** <なぜ>
2. **確認条件をGherkin Scenarioとして書く**(複数個。ストーリーが大きければScenarioの数が増えるだけでよい)。各Scenarioは:
   - `Given`(前提) / `When`(操作) / `Then`(期待される結果)
   - **ユーザーから観測可能な振る舞い・価値の言葉で書く。実装技術(特定のライブラリ・DB・アーキテクチャパターン等)には一切言及しない。**
   - 技術的な言及が混ざっていたら取り除き、「それは`gate-design`で決めるべき内容」とユーザーに伝える。
   - 曖昧な形容詞(「使いやすい」「速い」等)を避け、検証可能な形にする。
3. Non-Goals(このストーリーでやらないこと)を書く。
4. INVESTチェックを行う:
   - Independent, Negotiable, Valuable, Estimable, Small, Testable
   - **"Small"は「小さくあるべき」という圧力として扱わない**。大きくても1つの一貫した価値提供単位なら分割しない。
   - **分割の判断基準はサイズではなく「独立して価値をデリバリーできる一つのまとまりか」**。実質的に複数の独立した価値提供が1枚に混ざっている場合のみ、それぞれ意味のある単位に分割し、この手順を分割後の各ストーリーに対して繰り返す。
5. 簡易見積り(S/M/L/XL等)。大きい見積りそのものは問題ではない。
6. `.specs/stories/<story-slug>/STORY.md` を書く。
7. `.specs/BACKLOG.md` にこのストーリーを追加する(ステータス: ready)。
8. 次は `gate-design` に進むことを案内する。

## STORY.md フォーマット

```gherkin
Feature: <ストーリータイトル>
  As a <誰>
  I want <何>
  So that <なぜ>

  Scenario: <確認条件1の名前>
    Given <前提>
    When <操作>
    Then <期待される結果>

  Scenario: <確認条件2の名前>
    Given <前提>
    When <操作>
    Then <期待される結果>
```

```markdown
## Non-Goals
- ...

## INVESTチェック
- Independent: ...
- Negotiable: ...
- Valuable: ...
- Estimable: ...
- Small: ...(参考情報。分割の絶対基準にはしない)
- Testable: ...

## 見積り
<S/M/L/XL>

## ステータス
ready

## イテレーション
(未定)
```

## BACKLOG.md フォーマット(無ければ作成)

```markdown
# Backlog (Master Story List)

| Story | 優先度 | 見積り | ステータス | イテレーション |
|---|---|---|---|---|
| <story-slug> | ... | ... | ready | - |
```
