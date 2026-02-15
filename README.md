# Qiita / Zenn 記事管理リポジトリ

Qiita と Zenn の記事を 1 つのリポジトリで管理します。

## ディレクトリ構成

```text
.
├── articles/          # Zenn 記事
├── books/             # Zenn 本
├── images/            # Zenn 用画像
├── qiita/             # Qiita CLI 用プロジェクト
├── package.json       # Zenn CLI 依存
└── README.md
```

## 前提条件

- Node.js 18 以上
- npm

## セットアップ

```bash
# ルート (Zenn CLI)
npm install

# qiita
cd qiita
npm install
```

## 使い方

### Zenn

```bash
npx zenn new:article
npx zenn new:book
npx zenn preview
```

### Qiita

```bash
cd qiita
npx qiita login
npx qiita preview
npx qiita publish <basename>
```

## 記事のアップロード方法

### Zenn

- `articles/*.md` の front matter で `published: true` にした記事が対象です。
- `master` ブランチにマージされると、Zenn の GitHub 連携により自動でアップロードされます。
- Qiita のような手動 `publish` コマンドはありません。

### Qiita

- `qiita/` ディレクトリで `npx qiita publish <basename>` を実行してアップロードします。

## 参考

- [Zenn CLI ガイド](https://zenn.dev/zenn/articles/zenn-cli-guide)
- [Qiita CLI](https://github.com/increments/qiita-cli)
