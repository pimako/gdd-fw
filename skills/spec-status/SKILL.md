---
name: spec-status
description: .specs/配下の仕様駆動開発パイプラインの進捗状況を一覧表示する。ユーザーが「進捗を見せて」「spec-statusを確認したい」「今どのフェーズ?」と言った時に使う。
---

# spec-status — 進捗確認

## 手順

1. 対象プロジェクト直下の `.specs/` ディレクトリを探索する。存在しなければ、まだ仕様駆動開発パイプラインが開始されていない旨を伝え `spec-new` を案内する。
2. `.specs/` 配下の各 `<slug>/STATE.md` を読み、以下を機能ごとに一覧化する:
   - feature名 / slug
   - current_phase
   - フェーズ進捗チェックリストの状況(spec/design/tasks/implement/test/review/verify/retroのどこまで完了か)
   - updated_at
3. STATE.md に矛盾がある場合(例: current_phaseは進んでいるのに対応ファイルが存在しない)は、その旨を注記する。
4. 複数機能が並行している場合は表形式で見やすく提示する。
5. ユーザーが特定の機能について続きを進めたそうであれば、次に実行すべきスキル(未完了フェーズの先頭)を提案する。
