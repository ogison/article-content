---
title: Claude Code Routinesで「ラベル付きPRのマージを起点に次PRを作る」運用
tags:
  - ClaudeCode
  - GitHub
  - PullRequest
  - 自動化
private: false
slide: false
ignorePublish: false
---

## はじめに

本記事は、Claude Code公式ドキュメントのRoutinesページ（https://code.claude.com/docs/en/routines）をベースに、

- 保守系タスクのPRを定期/イベントで自動化
- PRにラベルを付与して制御
- 特定ラベル付きPRがマージされたときに、次のPR作成フローを起動

する実践パターンを紹介します。

> 前提: Routinesは **research preview** で、仕様や制限が変わる可能性があります。

---

## Routinesの要点（公式ドキュメント準拠）

まず押さえるべきポイントです。

- Routineは「保存された設定（プロンプト、対象リポジトリ、コネクタ等）」をクラウドで自律実行する仕組み
- トリガーは **Schedule / API / GitHub event** を併用可能
- GitHub triggerはWeb UIから設定（CLIではGitHub/APIトリガーの追加不可）
- GitHub eventは「一致したイベントごとに新規セッション」が作成される
- ラベルやマージ状態でPRフィルタが可能（Labels / Is merged など）

この「PRフィルタ」が、今回のラベル連鎖運用の中核です。

---

## 実現したい運用

### 目標

`maintenance` ラベル付きPRが **merged** されたら、Routineが次の保守PRを作る。

### コアアイデア

GitHub triggerを次の条件で絞り込みます。

1. Event: `pull_request.closed`
2. Filter: `Labels` に `maintenance` を含む
3. Filter: `Is merged` が `true`

これで「ラベル付きPRがマージされたときだけ」Routineが走ります。

---

## セットアップ手順（Web UI中心）

### 1. Routineを作成する

`claude.ai/code/routines` で `New routine` を作成し、以下を設定します。

- Prompt（後述）
- Repository（保守対象）
- Environment（必要なネットワーク/環境変数のみ）
- Connector（不要なものは外す）

### 2. GitHub triggerを追加する

Routine編集画面で `Add another trigger` → `GitHub event` を選択し、

- Event: `pull_request.closed`
- Filters:
  - Labels: `maintenance`
  - Is merged: `true`

を設定します。

> 補足: GitHub trigger利用には、対象リポジトリへのClaude GitHub Appのインストールが必要です。

### 3. 保守PRを作るためのプロンプトを定義する

Routineプロンプト例（最小）:

```text
あなたは保守タスク専用のエージェントです。

入力コンテキスト:
- 直近でマージされたPR情報
- リポジトリの現在状態

目的:
- 次の小さな保守タスクを1件選び、変更を実装し、PRを作成する

制約:
- 変更は小規模（1PR=1テーマ）
- 破壊的変更は禁止
- PRタイトルは `chore:` で開始
- PRには `maintenance` ラベルを付与
- PR本文に「目的 / 変更点 / リスク / 戻し方 / 検証結果」を記載
- テスト失敗時はPRを作らず、セッション内で失敗理由を明示
```

このプロンプトにすることで、次回の `maintenance` PRが再びマージされると、同じRoutineが継続的に起動します。

---

## 運用時のガードレール

### 1. 連鎖停止ラベルを用意する

例えば `stop-chain` を作り、Routineの指示に「`stop-chain` 付きPRは対象外」を加えると安全です。

### 2. Branch protectionで必ず人間レビューを残す

- Required checks
- Required reviews
- Auto-merge条件の制限

を設定して、完全自動マージにしない構成を推奨します。

### 3. preview制限を考慮する

公式には、research preview中のため**挙動・API・制限の変更可能性**と、GitHubイベントの**時間あたり上限**に関する記述があります。
運用前に、ルーチン詳細画面の使用量・制限表示を確認してください。

---

## よくある詰まりどころ

### Q1. CLIだけで完結できますか？

`/schedule` はスケジュールRoutine作成/更新には便利ですが、GitHub/APIトリガーの詳細設定はWeb UIで行います。

### Q2. 1つのPRで何度も走りませんか？

GitHub eventは一致イベントごとにセッションが作られます。
そのため、`pull_request.closed` + `Is merged=true` で、マージ時のみ起動するよう絞るのがポイントです。

### Q3. ラベル条件は本当に使えますか？

PRフィルタ項目に `Labels` があるため、ラベルゲート運用が可能です。

---

## まとめ

Claude Code RoutinesのGitHub triggerを使うと、

- `pull_request.closed`
- `Labels includes maintenance`
- `Is merged = true`

という条件で、**「特定ラベルのPRがマージされたら次のPR作成を開始する」**運用を実現できます。

まずは小さな保守作業（依存更新、軽微なリファクタ、ドキュメント修正）から始め、
ガードレールを入れて段階的に自動化範囲を広げるのがおすすめです。
