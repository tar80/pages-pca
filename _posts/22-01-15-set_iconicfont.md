---
layout: post
title: アイコンフォントをエントリに設定
version: PPxDW64182+04以降
date: 2022-01-15
comment: 投稿。
categories: PPc
---
### 説明
NerdFontsに含まれるアイコンフォントを拡張子アイコンの代わりに表示します。<BR>
拡張コメントにアイコンフォントをセットして表示する仕組みです。重めの処理なのでPCのスペックによって
は常用は厳しいかもしれません。前提として使用フォントがNerdFontsをサポートしていること、描画・速度面から
PPxDirectWrite版+ES6での運用のみを想定しています。
<BR><BR>

![sample]({{ site.baseurl }}{% link /public/img/set_iconicfont.png %})

> 画像のフォントはFirgeNerd 12pt

### 使い方
1. スクリプトの初期設定をする。
1. 表示を`アイコン(%@)`に変更。

#### 設定
```text
A_exec = {
scr  =    ;スクリプトをまとめておくディレクトリパス
}

KC_main = {
; 【n】は1-9で任意。
COMMENTEVENT【n】 , *script %'scr'%\setIconicFonts.js,【n】
}

MC_celS = {
アイコン(&@)    = u【n】,2 F18,6 zK7 S1 T9 s1
```
<BR>
<script src="https://gist.github.com/tar80/69060076f55068448e90e7702314c68b.js"></script>
