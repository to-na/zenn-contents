---
title: "ローコードツール Builder.io を Next.js で試してみた"
emoji: "💨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nextjs", "ローコードツール"]
published: false
---

# builder.io とは

海外製のローコードツールです。React など各種の言語/フレームワークの上に構築されるため、STUDIO などのノーコードツールで実現できない程度に複雑なアプリケーションに向いていると思います。

# クイックスタート

基本的に[公式のガイド](https://www.builder.io/c/docs/dev-quickstart)に従えば構築できますが、簡単に手順を書いておきます。

1. Builder.io のアカウントを作成
2. `npm init builder.io` を実行
3. 途中で認証を要求されるので、コンソールに表示された URL にアクセスして認証
4. builder.io 上のメニューから Model を選択し、Page Model の Editing URL に `http://localhost:3000` を設定
5. Builder.io 上の Account Settings から Public API Key を取得し、`_app.tsx` の `builder.init` 内にコピペ

## 注意点
