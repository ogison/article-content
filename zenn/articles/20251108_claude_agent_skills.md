---
title: "定型開発作業をClaude Code Agent Skillsに落とし込む"
emoji: "🤖"
type: "tech"
topics: ["anthropic", "claude", "AgentSkills", "automation", "開発効率化"]
published: true
---

# はじめに

Claude には、特定のタスク実行をより高精度かつ効率的に行うための機能として **Agent Skills** があります。  
Claude Code を使った開発が前提となりますが、この Skills を活用することで、日々の開発における定型作業を自動化し、チーム全体の開発効率を大幅に高めることができます。

# Agent Skills とは

[Agent Skills](https://www.claude.com/blog/skills) は、特定のタスクをスクリプトや md ファイルなどに落とし込んで、必要に応じて Claude がよしなに呼び出してくれる機能になります。
Agent Skills は、Claude の API, Claude の APP, Claude Code など各 Claude 製品で設定・利用することができます。

# 定型作業を Skills に落とし込む

日々の開発では、**何度も似たようなプロンプトを毎回書いている**場面が多くあります。
例えば、python の pytest のようなテストコードを書く例を挙げてみます。
Claude Code で 1 ラリーで作り切りたい時、次のような詳細なプロンプトを書くことがあります。

```.md
## 対象ソースコード
src/services/user_service.py

## 依頼
create_userメソッドのpytest形式のテストコードを書いてください。

## 考慮事項
- 正常系・異常系をそれぞれ含める
- 条件を網羅できるようにすること

## 参考例
{参考ソース}
```

このような「考慮事項」や「参考例」などを毎回明示的に記述するのは手間です。
そこで、これらを Skills に落とし込む ことで、一度定義しておけば以降は次のように短いプロンプトだけで済むようになります。

```.md
src/services/user_service.pyのcreate_userのテストコードを書いて
```

Claude は定義済みの Skills を自動的に呼び出し、前述のような考慮点を踏まえたテストコードを生成してくれます。さらに、この Skills をチーム内で共有すれば、誰が使っても同じような形式・品質のテストコードを生成できるようになります。

また、定期的に行うドメイン知識が必要な開発や運用作業も Skills に落とし込むことで、属人化を防ぎ、チーム全体で効率的に実行できるようになります。

このように、毎回同じようなプロンプトを入力する定型作業や、チームで統一したい内容などには Skills が適していると考えています。

# Skills の作り方のおすすめ

anthropics 公式が[skills の テンプレート集](https://github.com/anthropics/skills)を公開しています。その中にある[skill-creator](https://github.com/anthropics/skills/tree/main/skill-creator)が優秀です。

この Skills を設定する際、一定の型みたいなものが決まっているので、単に md ファイルを配置しても Skills を設定することができません。`skill-creator`を使えば、テンプレートに従って正しい構成の Skills を簡単に作成、修正できるので、量産することが容易になります。

公式が公開している`skill-creator`をそのまま`.claude/skills`配下に配置しましょう！

# まとめ

Claude Agent Skills を活用することで、定型的な開発作業を効率的に自動化することができます。従来は、Claude Code の開発で用いた良いプロンプトをチーム内で共有する手法もあったのかなと思うのですが、代わりにこの Skills に落とし込んで、裏でよしなに設定してもらうという方法もありなのかなと考えてます。

Skills を活用して、より快適な Claude Code 開発を実現しましょう！
