---
layout: post
title: setSel.js
version: PPx181以降
date: 2021-11-22
comment: 記事を書いた。
categories: Script
---
### 説明
一行編集上の文字列の選択範囲を正規表現で指定するスクリプト。selStr\.jsの後継版です。<BR>
元ネタは[5chPPxスレ](https://egg.5ch.net/test/read.cgi/software/1476708638/405-411)。

> selStr\.jsの引数指定では正確な範囲指定が出来なかったので名前を変えて作り直しました。<BR>
  また、この変更によって以下の記事が影響を受けます。<BR>
  - [fenrirscanを用いたパス補完]({{ site.baseurl }}{% post_url 19-11-07-complate_path %})
  - [テンプレートを使ったリネーム一括処理]({{ site.baseurl }}{% post_url 20-12-18-rename_template %})

## 使い方
- スクリプトの引数に`"(選択範囲の前の文字列)(選択する文字列)"`となるように正規表現を指定する。
- `()`の外の文字も指定可能。
- 空文字を送ったときは`選択範囲の前の文字列`の後ろにカーソルを移動。

例)文字列`abc123.txt`に対する引数の結果 ※縦線はカーソル位置<BR>
- `,"(\D*)(\d*)"`  abc<span style="background-color:blue;color:white;">123</span>.txt<BR>
- `,"(\D*)()"`  abc\|123.txt<BR>
- `,"(\D*)(\d*).js"`	エラーメッセージ[ setSel.js: no match. ]

<BR>
<script src="https://gist.github.com/tar80/842a0e5108f83fe1883231c97b62f54e.js"></script>
