---
title: "Claude Code たちをWebブラウザから操作できるようにしてみた（Git Worktree用）"
emoji: "🐇"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["AI", "Claude", "ClaudeCode"]
published: true
---
## TL;DR

- 複数の Claude Code を Web UI で管理する Claude Code Crew というツールを作った
- Git Worktree の運用が楽になる
- tmux より Web UI の方がいい人向けのツール
- 設定が楽
- 目に優しい

## これは何

複数の Claude Code のセッションを、Web UI で操作できるツールです。Git Worktree の運用を想定しています。
https://github.com/to-na/claude-code-crew

![Claude Code Crew](https://storage.googleapis.com/zenn-user-upload/1b63b8ae3314-20250613.png)

![Gif](https://storage.googleapis.com/zenn-user-upload/06f537b6aabc-20250613.gif)

### 主な機能

- ブラウザベースのターミナル（xterm.js）
- リアルタイムセッション状態管理（busy/waiting/idle）
- Git worktree の作成・削除・マージ

## 背景

Git Worktree でそれぞれの Claude Code に別々のタスクをさせるとき、tmux なり VSCode なりで管理すると思います。
ただ個人的には、tmux あまり馴染んでないので、よりリッチなUIで操作出来たほうがいいなあと思いました。そこで、ブラウザUIで複数の Claude Code たちを操作できるツールを作ってみました。
実装にあたっては、[こちらの記事](https://zenn.dev/kbwok/articles/33fad69555d005)を参考にしています。

## 使い方

前提として、Claude Code がインストールされている必要があります。

`npm i -g cleude-code-crew`

作業したいディレクトリで、 `claude-code-crew` すると、localhost:3001 で起動します。

## 機能
### Webブラウザから簡単に操作

### Create Worktree

- 新しい Worktree とディレクトリを作ります

### Merge Worktree
- worktree で作業が終わったら main ブランチなどにマージできます。Claude が commit したかは目視チェックしたほうがいいです。

### Delete Worktree
- いらなくなった worktree を消せます

## 技術スタック

### バックエンド
- Node.js + Express
- Socket.io（WebSocket通信）
- node-pty（擬似端末）

### フロントエンド
- React + TypeScript
- Material-UI
- xterm.js（ターミナルエミュレーション）

### アーキテクチャ
- 単一ポート設計（`claude-code-crew` するだけで起動）
- npm global install 対応

## よかったこと
- Claude Code たちの状態がわかりやすい
  - 暇: 緑色
  - 作業中: 黄色
  - ユーザー操作が必要: 水色
- localhost で動くので、Proxy すればスマホやタブレットからアクセスできる

## まだできないこと

- 通知。あると便利だと思いつつ、まだ実装できてないです。なるはやで作ります。
- tmux でもうちょっと複雑なことをしたい場合、少し柔軟性に欠けるかもしれません。

## 補足
本格稼働させるには Claude Max を契約したほうがいいと思います。

## 参考資料
Git Worktree について
https://zenn.dev/siu_issiki/articles/git_worktree

tmux で同様のことができるツール
https://zenn.dev/kbwok/articles/33fad69555d005