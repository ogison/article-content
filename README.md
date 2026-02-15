# Qiita / Zenn 記事管理リポジトリ

このリポジトリは、Qiita と Zenn の記事を 1 か所で管理するためのモノレポです。

- `qiita/`: Qiita CLI で管理する記事
- `zenn/`: Zenn CLI で管理する記事・本

## ディレクトリ構成

```text
.
├── qiita/
│   ├── .github/workflows/publish.yml  # Qiita 公開用 GitHub Actions
│   ├── public/                         # Qiita 記事
│   └── qiita.config.json
├── zenn/
│   ├── articles/                       # Zenn 記事
│   ├── books/                          # Zenn 本
│   ├── images/                         # Zenn 画像
│   └── package.json
└── README.md
```

## 前提条件

- Node.js 18 以上
- npm

## セットアップ

```bash
# zenn
cd zenn
npm install

# qiita
cd ../qiita
npm install
```

## 使い方

### Zenn

```bash
cd zenn
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

## 公開フロー

### Qiita

- `qiita/.github/workflows/publish.yml`: `push`（`main`/`master`）または手動実行で Qiita へ公開

### Zenn

- Zenn CLI には Qiita のような `publish` コマンドはありません。
- Zenn 側の GitHub 連携を有効にした状態で、`zenn/articles/*.md` の `published: true` を含む変更を `master` にマージすると公開されます。

## 参考

- [Zenn CLI ガイド](https://zenn.dev/zenn/articles/zenn-cli-guide)
- [Qiita CLI](https://github.com/increments/qiita-cli)
