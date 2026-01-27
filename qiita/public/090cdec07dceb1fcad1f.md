---
title: CodexでCoCo壱のMCPを作った話
tags:
  - MCP
  - codex
  - Deno
  - CoCo壱
private: false
updated_at: '2025-12-07T01:17:15+09:00'
id: 090cdec07dceb1fcad1f
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

近所に CoCo 壱があるので、よく行くのですが、どういう組み合わせで頼むか悩む時ありませんか？
おすすめの組み合わせを提示してくれるものがあったらいいなと思い、せっかくなら MCP を作成して実現できないかを試してみました。

:::note warn
**注意**  
ここで紹介している MCP は、私個人の学習目的で作った非公式ツールです。  
CoCo 壱番屋公式とは無関係なので、その点だけご理解ください。
:::

## Claude Desktop で試した例

対話形式で〇〇円以内でおすすめの組み合わせを提示してくれるものが作れました。

```
ユーザー：1300円で満足できるココ壱のメニューの組み合わせ教えて

Claude：1300円の予算でしたら、こんな組み合わせがおすすめです：
1位：牛すじ煮込みカレー（1297円）

牛すじ煮込みカレー（1132円）+ クリーミータルタルソース（74円）+ コーン（91円）
ライス：150gハーフサイズ（90円引）
辛さ：普通
```

https://x.com/ogison999/status/1987892535373824430?s=20

## 作成方針

先ほどの動画のように、値段やこちらの要望に応じて、おすすめの組み合わせを提示することができる MCP の作成を目指しました。

1. JSON 化したメニューを MCP から読み込み
2. 予算や要望（辛さ、トッピングなど）を入力として受け取り
3. 条件に合う組み合わせを選んで返す

個人利用の用途なので、ローカル MCP で作ることにしました。

## データ作成

https://www.ichibanya.co.jp/menu/pdf/menubook_regular.pdf
公式のメニューブックの PDF があるので、こちらをデータ化させます。
CoCo 壱のメニューデータを以下のような Json 形式にします。

```.json
{
  id: "1293",
  name: "ポークカレー",
  englishName: "Pork curry",
  price: 646,
},
{
  id: "1294",
  name: "甘口ポークカレー",
  englishName: "Mild pork curry",
  price: 646,
}
```

当初は、PDF データを読み込んで Codex に Json データを作らせていたのですが、価格や名前が誤りがあり、正しく Json に落とし込むことができませんでした。
PDF からの読み取りより、画像からの読み込みの方が精度が高くなりそうだなと考え、メニューデータをスクショして、それを ChatGPT に読み込ませて、Json 形式に変換させました。こちらのアプローチの方が問題なく Json 化できたので、分類ごと（カレーの種類、ライスの量、辛さの種類など）で Json 化を行いました。

## MCP 実装

ローカルで使う際にセットアップの手間を減らしたかったため、環境構築がほぼ不要で、そのまま実行できる Deno を使って MCP を作成しました。Deno は import するだけで依存モジュールを自動取得してくれるため、事前の npm install などが不要なのが便利でした。

以下のように、MCP で呼び出すことができるツールをいくつか作成します。この実装の部分は Codex に丸投げして作成しました。

```.ts
server.tool("searchMenuItems", {
  description: "CoCo壱のメニューを検索するツール",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "商品名やキーワード" },
      category: { type: "string", nullable: true },
      maxResults: { type: "number", default: 20 },
    },
    required: ["query"],
  },
  async invoke({ query, category, maxResults }) {
    // ここで items をフィルタしてレスポンスを返す
  },
});
```

https://github.com/ogison/cocoichi-menu-mcp

## さいごに

この MCP を使えば、CoCo 壱で何を頼むか悩まなくなるので、ぜひ使ってみてください！
