---
name: spec-team
description: プロジェクト単位でペルソナのロスター(.specs/PERSONAS.md)を設定・更新する。ユーザーが「レビューチームを決めたい」「ペルソナを設定したい」「/spec-team」と言った時に使う。spec-review/spec-verify/spec-discussが参照する土台。機能単位ではなくプロジェクト単位で一度設定すれば良い。
---

# spec-team — ペルソナロスターの設定

`personas/`(プラグイン同梱のライブラリ)から、このプロジェクトで実際に使うペルソナを選び、`.specs/PERSONAS.md` に記録する。機能ごとではなく、プロジェクトに対して一度設定すれば、以後の `spec-review` `spec-verify` `spec-discuss` が自動的に参照する。

## 手順

1. `personas/` ライブラリの一覧(role/goal/tier/phases)を確認する。
2. コアペルソナ(`product-owner`, `domain-specialist`, `tech-lead`, `security-engineer`, `qa-engineer`)はデフォルトで有効にする。
3. プロジェクトの特性についてユーザーに確認する(**AskUserQuestion**、複数選択可):
   - UIを持つか(→ `ux-reviewer`, 該当なら`accessibility-specialist`)
   - 機微データ・規制業界を扱うか(→ `legal-compliance`)
   - 本番運用・デプロイを伴うか(→ `sre-ops`, `release-manager`)
   - パフォーマンスがシビアか(→ `performance-analyst`)
   - データ基盤/機械学習が中心か(→ `data-engineer` / `ml-engineer`)
   - リソース制約のある環境(組み込み等)か(→ `embedded-systems-engineer`)
   - 多言語・複数地域展開を予定しているか(→ `localization-specialist`)
   - ドキュメント・外部公開APIの説明品質が重要か(→ `technical-writer`)
   - エンドユーザーの現場感覚を重視したいか(→ `end-user-advocate`)
4. `domain-specialist` は中身が空のテンプレートのため、このプロジェクトのドメイン(業界・分野・規制・専門知識)をユーザーに聞き、`.specs/personas/domain-specialist.md` として `personas/domain-specialist.md` の構成を踏襲しつつ具体的な内容で上書きする。
5. ライブラリにない視点が欲しい場合は、ユーザーに役割名・ゴール・バックストーリーを聞き、同じフォーマットで `.specs/personas/<custom-slug>.md` を作成する。
6. `.specs/PERSONAS.md` を以下の形式で書く。

   ```markdown
   # Personas

   ## 有効なペルソナ
   - product-owner (core)
   - domain-specialist (core, カスタム: .specs/personas/domain-specialist.md)
   - tech-lead (core)
   - security-engineer (core)
   - qa-engineer (core)
   - <選ばれたsemi/specializedペルソナ...>
   - <custom-slug> (custom: .specs/personas/<custom-slug>.md)

   ## 設定日
   <ISO日付>

   ## 備考
   ロスターを見直したい場合は `/spec-team` を再実行してください。
   ```

7. 設定内容をユーザーに要約して提示する。
