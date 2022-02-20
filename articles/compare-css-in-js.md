---
title: "styled-components に代わる CSS-in-JS の比較検討"
emoji: "👻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["JavaScript", "CSS", "React"]
published: true
---

# TL;DR

styled-components の代替として、[Compiled](https://compiledcssinjs.com/) は有力な選択肢

# Motivation

CSS-in-JS は様々なものがありますが、styled-components が有名でよく使われていると思います。styled-components に限らず、JS ファイル内でスタイルを書くことには十分な恩恵があります。例えば、

1. スタイルのスコープを制限できる
2. props によりスタイルを指定でき、似たスタイルを JS の記法で抽象化できる
3. 上記による開発体験の向上

などが挙げられます。
一方で、styled-components には 1 つ無視できない欠点があります。それは、**スタイルを JavaScript で定義する以上、JavaScript のファイルサイズが増大する**ということです。これによるアプリのパフォーマンスへの影響度は、アプリの規模や実装にも依るため一概には言えませんが、基本的に JavaScript ファイルが小さいに越したことはないと思います。したがって、他の CSS-in-JS を採用することを検討しました。

# 比較検討

## 観点

パフォーマンス面では、下記の２つの観点を重視しました。

- CSS extraction をサポートしているか
- より軽量な JS が生成されているか

CSS extraction は名前の通り、ビルド時に JS ファイルから CSS を抽出する技術です。これにより JS のサイズが軽減されることが期待できます。
また、別の観点として、開発体験や保守性も重要です。React においては [経年劣化に耐える ReactComponent の書き方](https://zenn.dev/takepepe/articles/howto-withstand-aging-react-component) にあるような Single File Component の書き方が個人的には開発体験が良かったので、この書き方ができるかどうかも観点に入れています。
その他、学習コストや、ライブラリの将来性や安定感の観点も検討します。

## CSS extraction

2022 年 2 月時点で、CSS extraction をサポートしている主な CSS-in-JS として、以下の 3 つが挙げられます。

- [Linaria](https://github.com/callstack/linaria)
- [vanilla-extract](https://vanilla-extract.style/)
- [Compiled](https://compiledcssinjs.com/)

今回は、CSS extraction の機能を使いたいため、この 3 つに絞りました。
なお、CSS extraction の欠点として、ユーザーの CSS キャッシュがない場合、実行時に スタイル を評価する場合に比べて FCP/LCP が低下するようです。(ref. [andreipfeiffer/css-in-js](https://github.com/andreipfeiffer/css-in-js#-performance-metrics))
したがって、サイトの特性やレンダリング方式によって、実際に CSS extract によってパフォーマンスが向上するか変わりうるのですが、例えば Compiled では Webpack の設定で CSS extraction の有効化が簡単に切り替えられるため、検証は比較的容易です。

## 開発体験・保守性

先述したように、Single File Component として実装したいのですが、vanilla-extract では、`.css.ts` ファイルを別途作成する必要があったため、vanilla-extract は選択肢からは外し、Compiled と Linaria の２つに絞りました。

## 生成される JS が軽量かどうか

以下のリポジトリのベンチマークをもとに、同等の機能の Compiled のコンポーネントを追加し、ビルド結果を確認しました。
https://github.com/geeky-biz/css-in-js-benchmark
ビルド結果

```
Page                                       Size     First Load JS
┌   /_app                                  0 B            69.4 kB
├ ○ /404                                   2.83 kB        72.2 kB
├ ○ /compiled                              1.41 kB        70.8 kB
├   └ compiled-css.css                     256 B
├ ○ /linaria                               3.23 kB        72.6 kB
├   └ pages/linaria.css                    170 B
├ ○ /regularcss                            1.27 kB        70.7 kB
├   └ pages/regularcss.css                 772 B
└ ○ /styled-components                     14.5 kB        83.9 kB
+ First Load JS shared by all              69.4 kB
  ├ chunks/framework-f32f173a83577278.js   42 kB
  ├ chunks/main-77cb722008623919.js        25.3 kB
  ├ chunks/pages/_app-ba3e4e931cd4f952.js  575 B
  ├ chunks/webpack-d1550f433b886c11.js     1.5 kB
  └ pages/_app.css                         170 B
```

実装したコンポーネントに対して生成される JS と CSS のファイルサイズは、Compiled の方が Linaria より半分程度のサイズという結果になりました。ただし、今回のベンチマークは軽量なコンポーネントを対象としているので、より規模の大きいコンポーネントだと結果が異なる可能性はあります。
なお、Compiled の CSS extraction を使わない場合も試してみましたが、この場合でも Compiled の方が Linaria より JS ファイルが小さいという結果になりました。

```
Page                                       Size     First Load JS
...
├ ○ /compiled                              2.07 kB        71.5 kB
├ ○ /linaria                               3.2 kB         72.7 kB
├   └ css/3030cf0462c43300.css             141 B
```

以上より、この時点で Compiled が有力な選択肢となりました。

## Compiled の学習コスト・将来性・安定性

最後に Compiled に対して、学習コストや将来性・安定性の観点をチェックしました。
まず、Compiled は他の CSS-in-JS と記法はあまり変わらないため、styled-component などに慣れていれば学習コストは低いです。
また、[ドキュメント](https://compiledcssinjs.com/docs/)も比較的充実しています。
一方で、2020 年にリリースされた後発のライブラリであるため、Web 上に情報が少なく、Compiled 特有のバグを踏んだときに、自力で解決する必要があるかもしれません。
将来性・安定性については、Compiled は Attlasian が中心となって開発しています。他のライブラリに比べて後発であることもあり、Linaria や vanilla-extract に比べて GitHub の stars は現状少ないのですが、リリース頻度が高く安定感はあります。

## Conclusion

styled-components の代替として、Compiled は良い選択肢だと言えます。
使用感についても今後記事にしたいと思います。
