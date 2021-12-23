---
layout: post
title: waitRun.js
version: PPx182以降
date: 2021-12-23
comment: 投稿。
categories: Script
---
### 説明
`*run -wait:idle` `%Oi`が反映されないプログラムの起動待機スクリプト。<BR>
wt.exe用に作りました。初回実行と二回目以降で起動時間に大きな差があるプログラムで有用と思われます。

> スクリプト内で`%*findwindowclass`(message module)を使用しています。

### 使い方
- `対象プログラム %: %'scr'%\waitRun.js,【待機秒】,【クラス名】%: 起動後実行するコマンド`を実行。
  - 第一引数はミリ秒で指定。スクリプトは指定した秒ごとに起動確認する。
- 起動時エラーの対応として、50回確認して起動していない場合はスクリプトを終了する。

#### 設定
```
A_exec = {
scr  =    ;スクリプトをまとめておくディレクトリパス
}
```
<BR>
<script src="https://gist.github.com/tar80/1c46d7f0700e495c5c6f515df52a5e22.js"></script>
