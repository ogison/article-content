---
title: 公式ドキュメントから学ぶClaude Codeを使いこなすための実践Tips集
tags:
  - CLI
  - 開発ツール
  - AI
  - Claude
  - ClaudeCode
private: false
updated_at: '2025-08-12T23:59:17+09:00'
id: 5cd62270112cdebdfed0
organization_url_name: null
slide: false
ignorePublish: false
---

## 1. はじめに

Claude Codeを使って、公式Tipsをまとめさせました。Claude Codeの公式ドキュメントをもとに、Claude Codeで検索、収集、記事作成を行いました。

## 2. 基本操作を効率化するTips

### 2.1 Extended Thinking（深い思考モード）を活用しよう

複雑な問題解決やアーキテクチャ設計時には、Extended Thinkingを活用することで、より詳細な分析を得ることができます。

```bash
# トリガーフレーズ例
「このバグについてよく考えて」
「think harder about this architecture」
「このパフォーマンス問題を深く分析して」
```

Claude Codeは思考プロセスを灰色のイタリック体で表示し、通常よりも詳細な分析を実行してくれます。

### 2.2 @記法でファイルを効率的に参照する

特定のファイルを参照する際は、@記法を使うことで効率的に指示できます。

```bash
# 単一ファイルの参照
「@src/components/Header.tsx のスタイルを改善して」

# 複数ファイルの参照
「@package.json と @tsconfig.json の設定を確認して」

# ディレクトリの参照
「@src/components/ 内のコンポーネントを最適化して」
```

### 2.3 マルチライン入力を使いこなす

複数行のコードやテキストを入力する際の効果的な方法：

1. **バックスラッシュエスケープ** (`\` + `Enter`) - 最も確実
2. **macOSデフォルト** (`Option` + `Enter`) - macOSで標準的
3. **代替オプション** (`Shift` + `Enter`) - ターミナル設定に依存

## 3. スラッシュコマンド活用術

### 3.1 便利な組み込みコマンド

開発作業を効率化する組み込みコマンドを活用しましょう。

| コマンド | 機能 | 使用例 |
|---------|------|---------|
| `/help` | 利用可能なコマンド一覧を表示 | `/help` |
| `/clear` | 会話履歴をクリア | `/clear` |
| `/cost` | 現在セッションのコスト表示 | `/cost` |
| `/commit` | Gitコミットを作成 | `/commit` |
| `/pr` | プルリクエストを作成 | `/pr` |
| `/review` | コードレビューをリクエスト | `/review` |
| `/compact` | 会話コンテキストを圧縮 | `/compact` |

### 3.2 カスタムスラッシュコマンドの作成

繰り返し実行するタスクを自動化するために、カスタムコマンドを作成できます。

#### 3.2.1 設定場所
- **プロジェクト固有**: `.claude/commands/`
- **ユーザー共通**: `~/.claude/commands/`

#### 3.2.2 実用的なカスタムコマンド例

**包括的コードレビュー** (`.claude/commands/code-review.md`):
```markdown
---
description: 包括的なコードレビューを実行
model: opus
allowed_tools: ["read", "grep", "bash"]
---

包括的なコードレビューを実行します。

## レビュー項目
1. コード品質とベストプラクティス
2. セキュリティの脆弱性
3. パフォーマンスの問題
4. テストカバレッジ
5. ドキュメント

$ARGUMENTS
```

**テスト実行と分析** (`.claude/commands/test.md`):
```markdown
---
description: プロジェクトのテストを実行し結果を分析
---

!npm test

テスト結果を分析して、失敗したテストがあれば修正案を提案してください。
```

## 4. IDE統合で生産性向上

### 4.1 キーボードショートカットを活用

IDE統合を使用することで、開発フローを中断することなくClaude Codeを利用できます。

- **Mac**: `Cmd+Esc`
- **Windows/Linux**: `Ctrl+Esc`

### 4.2 効果的なワークフロー

```bash
# 朝の開始時
cd /project
claude
/ide  # IDE接続

# 作業中のクイック起動
Cmd+Esc  # または Ctrl+Esc
「このエラーを修正して」

# レビュー時
「最新の変更をレビューして」
```

### 4.3 複数プロジェクトの管理

各プロジェクトで個別のClaude Codeセッションを維持：

```bash
# プロジェクトA
cd /project-a
claude

# 別のターミナルでプロジェクトB
cd /project-b
claude
```

## 5. 設定とカスタマイズのベストプラクティス

### 5.1 CLAUDE.mdファイルの効果的な書き方

プロジェクト固有の情報をCLAUDE.mdファイルに記録することで、Claude Codeがプロジェクトの文脈を理解できます。

```markdown
# プロジェクト規約
- TypeScriptを使用
- 関数型プログラミングを優先
- テストはVitestで記述
- コミットメッセージは英語で記述

# よく使うコマンド
- ビルド: `npm run build`
- テスト: `npm test`
- リント: `npm run lint`
- 開発サーバー: `npm run dev`

# アーキテクチャ
- /src/components: UIコンポーネント
- /src/services: ビジネスロジック
- /src/utils: ユーティリティ関数
- /src/hooks: カスタムReactフック

# 命名規則
- コンポーネント: PascalCase
- 変数・関数: camelCase
- 定数: UPPER_SNAKE_CASE
- ファイル名: kebab-case
```

### 5.2 権限設定による安全な利用

`~/.claude/settings.json`で適切な権限設定を行いましょう。

```json
{
  "permissions": {
    "fileOperations": {
      "allowed": true,
      "writeAccess": true,
      "deleteAccess": false,
      "excludePaths": [".env", "*.key", "secrets/*"]
    },
    "webAccess": {
      "allowed": true,
      "allowedDomains": ["github.com", "docs.anthropic.com"]
    },
    "codeExecution": {
      "allowed": true,
      "languages": ["python", "javascript", "bash"],
      "timeout": 30000
    }
  }
}
```

### 5.3 開発環境別の設定テンプレート

**開発環境用**:
```json
{
  "model": "claude-3-sonnet",
  "permissions": {
    "fileOperations": { "allowed": true },
    "codeExecution": { "allowed": true },
    "webAccess": { "allowed": true }
  },
  "logging": { "level": "debug" },
  "autoUpdates": { "enabled": true }
}
```

**本番環境用**:
```json
{
  "model": "claude-3-sonnet",
  "permissions": {
    "fileOperations": {
      "allowed": true,
      "excludePaths": ["*.key", ".env*", "secrets/*"]
    },
    "codeExecution": { "allowed": false }
  },
  "security": { "requireConfirmation": true },
  "audit": { "enabled": true }
}
```

## 6. フック機能による高度な自動化

⚠️ **注意**: フック機能は自動的にシェルコマンドを実行するため、セキュリティリスクがあります。自己責任で使用してください。

### 6.1 Git自動コミットの設定

作業完了時に自動的にコミットを作成：

```json
{
  "hooks": {
    "Stop": [{
      "command": "git add -A && git commit -m 'Auto-commit by Claude Code' || true",
      "timeout_ms": 10000
    }]
  }
}
```

### 6.2 セキュリティチェックの自動化

危険なBashコマンドをブロック：

```json
{
  "hooks": {
    "PreToolUse": [{
      "tool_matchers": ["Bash"],
      "command": "bash -c 'if echo \"{{tool_input}}\" | grep -q \"rm -rf\"; then echo \"危険なコマンドをブロックしました\" && exit 1; fi'",
      "timeout_ms": 2000
    }]
  }
}
```

### 6.3 ファイル変更の記録

```json
{
  "hooks": {
    "PostToolUse": [{
      "tool_matchers": ["Edit", "Write"],
      "command": "echo \"$(date): {{tool_name}} - {{tool_input}}\" >> ~/.claude/file_changes.log",
      "timeout_ms": 1000
    }]
  }
}
```

## 7. インタラクティブモードの活用

### 7.1 Vimモードで高速編集

`/vim`コマンドでVimモードを有効化し、慣れ親しんだVimコマンドで効率的に編集：

```
# 基本的なVimコマンド
h j k l  # 移動
w e b    # 単語ナビゲーション
0 $ ^    # 行ナビゲーション
dd x i   # 編集コマンド
```

### 7.2 履歴機能の活用

- `↑` / `↓` でコマンド履歴をナビゲート
- `Ctrl+R` で履歴の逆方向検索
- 作業ディレクトリごとに履歴を保存

## 8. SubAgent機能でワークフローを専門化

### 8.1 SubAgentとは

SubAgentは、Claude Code内で特定のタスクを処理するために設計された特殊なAIアシスタントです。独立したコンテキストウィンドウを持ち、専門的なタスクに特化した処理が可能です。

#### 8.1.1 主要な特徴

1. **独立したコンテキスト**
   - 各SubAgentは独自のコンテキストウィンドウで動作
   - メインセッションのコンテキストを消費しない
   - タスク完了後、結果のみがメインセッションに返される

2. **カスタマイズ可能**
   - 特定の役割と振る舞いを定義
   - 必要なツールのみにアクセスを制限
   - プロジェクトやユーザー固有の設定が可能

3. **自動委任**
   - Claude Codeが適切なSubAgentを自動選択
   - タスクの内容に基づいた最適な選択
   - シームレスなワークフロー統合

### 8.2 SubAgentの作成と設定

#### 8.2.1 ファイル構造

**プロジェクトレベル**:
```
.claude/agents/
├── code-reviewer.md
├── test-writer.md
└── documentation-specialist.md
```

**ユーザーレベル**:
```
~/.claude/agents/
├── personal-assistant.md
└── learning-companion.md
```

#### 8.2.2 基本的な定義構造

```markdown
---
name: code-reviewer
description: コードレビューの専門家。ベストプラクティス、セキュリティ、パフォーマンスを重視
tools: Read, Grep, Glob, Bash
---

# Code Review Specialist

あなたは経験豊富なコードレビュアーです。以下の観点でコードを徹底的にレビューします：

## レビュー基準
1. **コード品質**
   - 可読性と保守性
   - DRY原則の遵守
   - SOLID原則の適用

2. **セキュリティ**
   - 脆弱性の検出
   - 入力検証
   - 認証と認可

3. **パフォーマンス**
   - アルゴリズムの効率性
   - リソース使用の最適化
   - スケーラビリティ
```

### 8.3 実践的なSubAgent例

#### 8.3.1 テスト専門SubAgent

```markdown
---
name: test-writer
description: 包括的なテストケースを作成するテスト専門家
tools: Read, Write, Edit, Bash
---

# Test Writing Expert

あなたはテスト駆動開発（TDD）の専門家です。

## 責任範囲
- ユニットテストの作成
- 統合テストの設計
- エッジケースの特定
- テストカバレッジの最適化

## テスト作成ガイドライン
1. Arrange-Act-Assert パターンの使用
2. 明確で説明的なテスト名
3. 独立したテストケース
4. モックとスタブの適切な使用
```

#### 8.3.2 セキュリティ監査SubAgent

```markdown
---
name: security-auditor
description: セキュリティ脆弱性の検出と修正提案
tools: Read, Grep, Glob
---

# Security Audit Specialist

## 監査チェックリスト

### 1. 入力検証
- SQLインジェクション
- XSS攻撃
- コマンドインジェクション

### 2. 認証と認可
- パスワード強度
- セッション管理
- アクセス制御

### 3. データ保護
- 暗号化の実装
- 機密情報の露出
- ログの適切な処理
```

### 8.4 SubAgentの活用テクニック

#### 8.4.1 自動委任と明示的呼び出し

**自動委任**:
```bash
# Claude Codeが自動的に適切なSubAgentを選択
「このコードをレビューしてください」
# → code-reviewerが自動起動
```

**明示的呼び出し**:
```bash
# 特定のSubAgentを直接指定
「test-writerを使ってこの機能のテストを書いてください」
```

#### 8.4.2 チェーン実行による連携

```bash
「リファクタリング後、テストを書いて、ドキュメントを更新してください」
# 1. refactoring-expert
# 2. test-writer  
# 3. documentation-specialist
```

#### 8.4.3 ツールアクセス制限

```yaml
# すべてのツールを継承
tools: *

# 特定のツールのみ
tools: Read, Grep, Glob

# ツールごとの権限
tools:
  - Read: unlimited
  - Write: project_only
  - Bash: safe_commands_only
```

## 9. まとめ

Claude Codeを使って、公式ドキュメントの情報をまとめてみました。今回は、公式ドキュメントの各ページを個別でmdファイルにまとめて、それらをもとに記事の作成をしました。改めて、今Claude Codeでより精度の高い出力を出す方法はどういうものがあるかを簡単におさらいすることができてよかったです。
