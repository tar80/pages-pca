---
layout: post
title: マークとハイライトをトグル
version: ppx181以降
date: 2021-11-14
comment: スクリプトを書き直した。
repository: tar80/ppm-misc
categories:  PPc @plugin
---
### 説明
エントリの変更状態とハイライトの色番号を保持しつつマークとハイライトをトグルするスクリプト。
- エントリごとの色情報は%si"enhl"にJSON形式で保持されます。
- JScript版は連続実行するとPPcがハングアップすることがあるので<span style="color:red;">非推奨</span>。

### 使い方
- スクリプトを実行。
- 第一引数に番号を指定すると色番号を持たない(マークしただけの)エントリのハイライト色を指定できる。

### 設定
<script src="https://gist.github.com/tar80/bca4b9e669eefa5d1d9f33d30372b5bf.js"></script>
