---
title: Claude CodeのToken Costを抑えるための実践Tips集
tags:
  - AI
  - LLM
  - Claude
  - ClaudeCode
private: false
updated_at: '2026-01-28T00:19:52+09:00'
id: d0645cf1a7a3c86395a3
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

Claude Codeを使っていると、

* 「思ったよりトークン消費が多い」
* 「長時間使うとコストが気になる」

と感じることがあります。

Claude Codeでは **入力・出力・コンテキストすべてがトークンとして課金対象** になるため、
使い方次第でコストに大きな差が出ます。

この記事では、公式ドキュメント
[https://code.claude.com/docs/en/costs#reduce-token-usage](https://code.claude.com/docs/en/costs#reduce-token-usage)
の内容をベースに、**Claude CodeのToken Costを抑えるためのポイント**を日本語でまとめます。

## Tips集
紹介するTIps集は以下になります。

1. `/clear`や`/compact`を利用する
2. モデルを使い分ける
3. MCPサーバー・ツール定義を整理する
4. プラグインを利用する
5. フックとスキルを利用する
6. CLAUDE.mdからスキルへ移動
7. サブエージェントを利用する
8. プロンプトを具体的に書く

## 1. `/clear`や`/compact`を利用する

### 不要な履歴は `/clear` で削除する

タスクが変わったにも関わらず、過去の会話を引きずると
毎回その履歴分のトークンを消費します。

```bash
/clear
```

### 自動要約（`/compact`）を活用する

Claude Codeは長くなった履歴を自動で要約（auto-compact）します。

さらに、要約時に **残したい情報を指示** することも可能です。

```bash
/compact Focus on code samples and API usage
```

Claude.mdに以下の指示を記載する
```md
# Compact instructions

When you are using compact, please focus on test output and code changes
```

## 2. モデルを使い分ける

Claude Codeでは複数のモデルを利用できます。

| モデル    | 特徴                      |
| ------ | ----------------------- |
| Sonnet | コストと性能のバランスが良く、日常的な開発向け |
| Opus   | 高性能だが高コスト。設計や難しい推論向け    |
| Haiku  | 軽量で安価。小さなタスクや補助作業向け     |

* 常に最上位モデルを使わない
* タスクに応じてモデルを切り替える

ことでトークンコストを抑えられます。


## 3. MCPサーバー・ツール定義を整理する

Claude Codeでは、MCPサーバーやツール定義もコンテキストに含まれます。

### 対策例

* `/context` で現在のコンテキストを確認
* 不要なMCPサーバーを `/mcp` で無効化
* CLIツール（gh, aws, gcloud など）を優先利用

不要な定義を減らすことで、**見えないトークン消費**を防げます。

## 4. プラグインを利用する
プログラミング言語（Python/TypeScript/Javaなど）向けのコードインテリジェンスプラグインがあります。grep検索するかわりに、ソース上の定義や呼び出し元に飛んで利用Tokenを抑えることができます。
`/plugin`コマンドで以下に記載がある該当のプラグインをインストールできます。
https://code.claude.com/docs/en/discover-plugins#code-intelligence

## 5. フックとスキルを利用する
カスタムフックを利用すれば、利用トークン量を減らせる場合があります。数万行のログファイルからエラー箇所を探す際、`grep`でエラー箇所を探すことができるBashコマンドをフックに設定して利用できます。

また、レビュー観点のスキルを作成することで、より注目すべき箇所を見てレビューができるので、不要な箇所を閲覧する手間を減らしてレビューを進めることができます。

## 6. CLAUDE.mdからスキルへ移動
Claude.mdファイルはセッション開始時に読み込まれるので、特定のタスクに関する指示の記載がある場合は、スキルに移動させた方がいいです。公式曰く、500行程度に抑えるのがベストです。

## 7. サブエージェントを利用する
テストの実行、ドキュメントの取得などは多くのコンテキストを消費する可能性があるので、この処理をサブエージェントに委任することで、メインの会話履歴に不要な情報を残さないで会話を進めることができます。

## 8. プロンプトを具体的に書く

曖昧な指示は、Claudeが広範囲の情報を読み込む原因になります。

### 悪い例

```text
Improve this codebase
```

### 良い例

```text
Add email validation to login.ts function only
```

* 対象ファイル
* 作業内容
* 変更範囲

を明確にすると、トークン消費も最小限になります。

## まとめ

Claude CodeのToken Costを抑えるポイントは以下です。

* 不要な履歴は持たない
* コンテキストを意識して管理する
* モデルを適切に使い分ける
* 指示は具体的・限定的に書く
* 出力を必要最小限にする

**「Claudeに考えさせすぎない設計」** が、
コスト削減と開発効率向上の両立につながります。

## 参考リンク

* [https://code.claude.com/docs/en/costs#reduce-token-usage](https://code.claude.com/docs/en/costs#reduce-token-usage)
