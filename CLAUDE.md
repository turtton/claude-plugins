# Review Gate System

## Overview

このプロジェクトでは、実装前に必ずレビューゲートを通過するワークフローを採用しています。
`/spec` → `/tasks` → `/impl` の3ステップで、要件定義→設計→タスク分解→実装の各フェーズで人間のレビューを強制します。
小さな変更はコマンドを使わず、通常の plan mode で対応してください。

## ワークフロー

```
/spec "機能の説明"
  → 要件整理 → エージェントレビュー → ユーザ承認
  → 設計提案 → エージェントレビュー → ユーザ承認
  → .review/{slug}.md に保存

/tasks {slug}
  → タスク分解 → エージェントレビュー → ユーザ承認
  → .review/{slug}.md に追記

/impl {slug} [task-numbers]
  → plan mode で実装開始
  → 完了後、CLAUDE.md/README に知見を反映して .review/ を削除
```

## Phase 遷移

| 操作 | 前提条件 | 遷移先 |
|------|---------|--------|
| /spec | なし | phase: design |
| /tasks | phase: design 以降 | phase: tasks-ready |
| /impl | phase: tasks-ready 以降 | phase: implementing → done |

## ルール

- `/spec` 経由で始まったタスクは、各フェーズでユーザの明示的な承認("ok", "lgtm", "進めて" 等)がない限り次に進まないこと
- 実装フェーズでは必ず plan mode を使うこと
- `.review/` ディレクトリは作業用の一時領域。実装完了後に削除すること
- 実装後に残すべき情報は CLAUDE.md または README.md に統合すること
- レビューエージェント（requirements-reviewer, design-reviewer, tasks-reviewer）は読み取り専用。ファイルを変更してはならない
- 実装エージェント（impl-agent）は .review/ ディレクトリを編集してはならない。チェックボックスの更新はコマンド側で行う

## プラグイン検証

plugin.json や marketplace.json を変更した場合、以下のコマンドで検証すること:

```bash
# プラグイン単体の検証
claude plugin validate plugins/review-gate

# マーケットプレイス全体の検証
claude plugin validate .
```