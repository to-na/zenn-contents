---
title: "軽量な祝日判定JSライブラリを作った"
emoji: "🛴"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["祝日", "JavaScript", "TypeScript", "npm"]
published: true
---

# 背景

日本の祝日を取得・判定したいことはしばしばあると思いますが、JavaScript の祝日関連ライブラリにはサイズが大きいものが多く、より軽量なものが欲しかったので実装しました。

# 軽量化のための工夫

**2022 年以前のデータについては、思い切って省略しています**（厳密には、判定はできるが正確ではない）
例えば、最近の祝日である日をカレンダーに反映したいだけの場合、高々前後 1 年だけ正確であれば十分と思われます。
また、年ごとの祝日をすべてベタ書きすることは避けています。

さらに、ロジックについても最小限にしています。結果として、現状 5KB 程度のサイズに収まっています。ただし、関数が少ないため、今後使い勝手の良い関数を増やす予定です。

# アルゴリズム

以下の方法で祝日を計算しています。

1. 固定の祝日を定数として定義
2. 移動する祝日を計算する関数を定義（成人の日、春分の日など）
3. 振替休日を計算する関数を定義
4. 与えられた `year` に対し、1, 2, 3 の結果を結合し、返す

外部ライブラリを使っていないため、他言語でも容易に実装可能と思われます。

# インストール

`npm i holiday-jp-since`
or
`bun install holiday-jp-since`

# 使い方

```ts
import { getHolidaysOfYear, getHolidaysBetweenYears } from "holiday-jp-since";
// 2024年の祝日の一覧を返す
const hoildays2024 = getHolidaysOfYear(2024);
// 2024年から2099年までの祝日の一覧を返す
const holidays2024To2099 = getHolidaysOfYear(2024, 2099);
```

返り値のフォーマットは、

```ts
[{
  holidayType: "fixed",
  name: "元日",
  year: 2024,
  month: 1,
  day: １,
}, ...]
```

のようにしています。
`holidayType: "fixed"` は年によらず固定の祝日であることを表しています。他にも、`moving, furikae` などがありますが、そもそも`holidayType` の使用頻度は多くないかもしれません。

# 課題

祝日は変動することがあるため、何らかの方法で更新をかける必要があります。例えば、[ケンオールが公開している祝日 API](https://kenall.jp/features/holidays)を定期的に叩いて、差分があれば通知する GitHub Actions を作ると良いと思われます（祝日はそうそう変わらないので、動作確認が難しそうですが）
