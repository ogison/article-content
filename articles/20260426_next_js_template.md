---
title: "私のNext.jsテンプレート"
emoji: "🚀"
type: "tech"
topics: ["nextjs", "react", "typescript", "tailwindcss", "個人開発"]
published: true
---

## はじめに

Claude CodeやCodexを使って、ちょっとしたツールやWebアプリを作ることが多く、その際Next.jsを使うことが多かったので、自分専用のNext.jsのテンプレートを用意しました。どういうテンプレートを用意したかご紹介します。

https://github.com/ogison/next-js-template

## 構成

構成は、Next.js App Router + TypeScript をベースにしています。スタイリングには Tailwind CSS v4 を利用し、UIコンポーネントは shadcn/ui を使う前提の構成にしています。
shadcn/ui は必要なコンポーネントをプロジェクト内に取り込んで管理するため、個人開発でもカスタマイズしやすく、AI視点でもどういうUIを利用しているか把握しやすい状態になっている点が気に入っています。

## パッケージマネージャ

`pnpm`を採用しています。個人で開発する規模だと、特に影響はないかもしれませんが、シンプルに`npm`や`yarn`よりもインストール速度が速いので`pnpm`を利用しています。

JavaScriptのベンチマークでも`pnpm`が`npm`や`yarn`よりもパフォーマンスがいい結果が残っています。
https://pnpm.io/benchmarks

## Next.jsのフォルダ構成

Next.jsのフォルダ構成は機能単位のコンポーネントをfeaturesフォルダで切る方式を採用しています。
appフォルダ配下はルーティングうやレイアウトなど、App Routerに関係する処理のみにして、画面内のUIやコンポーネント系などのアプリケーションの処理はcomponents配下で処理を書くようにしています。

```
src/
├── app/
│   ├── layout.tsx              # Root layout
│   ├── globals.css             # Global styles
│   └── (routes)/               # URLには含まれない Route Groups
│       ├── page.tsx            # /
│       ├── dashboard/
│       │   └── page.tsx        # /dashboard
│       └── settings/
│           └── page.tsx        # /settings
│
├── components/
│   ├── ui/                     # shadcn/ui のコンポーネント
│   └── features/               # 機能単位のコンポーネント
│
├── lib/
│   ├── utils.ts                # cn などの共通utility
│   └── constants.ts            # 定数
│
├── hooks/
│   └── use-*.ts                # Custom Hooks
│
└── types/
    └── *.ts                    # 共通型定義
```

## AI開発エージェント用のMDファイル

Claude CodeやCodexなど、複数のAI開発ツールを使って開発を進めていく中で、コンテキストファイルを管理が手間になるので、こちらの{記事}(https://zenn.dev/explaza/articles/33f1dd2003c981)を参考にして、シンボリックリンクを使って、1箇所にまとめるようにしています。1つにまとめると、スキルやコマンドでモデルの指定がしづらくなる（このスキルはClaude Haiku 4.5を使うなど）デメリットはりますが、今のちょっとした開発だと、不要なので汎用なスキル、コマンドを利用しています。

```
.
├── .agents/
│   ├── AGENTS.md
│   │   # AI開発エージェント向けの共通ルール
│   │
│   ├── commands/
│   │   └── *.md
│   │       # 共通で管理するカスタムコマンド
│   │
│   └── skills/
│       └── */
│           └── SKILL.md
│               # 共通で管理するカスタムスキル
│
├── AGENTS.md -> .agents/AGENTS.md
│   # Codex などが参照するルート配置のルールファイル
│
├── CLAUDE.md -> .agents/AGENTS.md
│   # Claude Code 用
│
├── .codex/
│   └── AGENTS.md -> ../.agents/AGENTS.md
│       # Codex 用
│
└── .claude/
    ├── commands/ -> ../.agents/commands
    │   # Claude Code 用カスタムスラッシュコマンド
    │
    └── skills/ -> ../.agents/skills
        # Claude Code 用カスタムスキル
```

## 最低限のCI

PR作成時、ビルドが通るかどうかを最低限チェックするCIを設定しています。普段の個人開発のためというよりかはどちらかというと、後続のdependabotの自動マージのために設定しています。

```yml
name: CI

on:
  pull_request:
    branches: [main]

permissions:
  contents: read

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v6

      - uses: pnpm/action-setup@v6
        with:
          version: 10

      - uses: actions/setup-node@v6
        with:
          node-version-file: .node-version
          cache: pnpm

      - run: pnpm install --frozen-lockfile

      - run: pnpm lint

      - run: pnpm format:check

      - run: pnpm build
```

## dependabot

dependabot は npm パッケージを対象に、毎週月曜日の朝9時に更新確認するようにしています。major update は破壊的変更が入る可能性があるため、自動更新対象からは除外しています。minor / patch update については、CIが通ったら自動で squash merge されるようにしています。

```yaml
version: 2

updates:
  - package-ecosystem: "npm"
    directory: "/"

    schedule:
      interval: "weekly"
      day: "monday"
      time: "09:00"
      timezone: "Asia/Tokyo"

    open-pull-requests-limit: 5

    # major update は自動更新対象から除外
    ignore:
      - dependency-name: "*"
        update-types:
          - "version-update:semver-major"

    labels:
      - "dependencies"
      - "dependabot"
      - "automerge"

    commit-message:
      prefix: "chore"
      include: "scope"
```

## 今後追加したいこと

まだまだ、AIの活用に合わせたテンプレートには改善できる余地があると思っています。
よく使うスキルやAI開発エージェントの設定をあらかじめ配置して、よりVibe Codingしやすい状態にしたいです。今後の普段の開発でこれ追加したほうがいいなと感じたものがあれば、随時アップデートしたいと思います！
