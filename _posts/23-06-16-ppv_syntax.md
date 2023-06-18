---
layout: post
title: batを使ってPPvで構文ハイライト
version: PPx191+2以降
date: 2023-06-18
comment: 記事を修正。
repository: tar80/ppm-view
categories: PPc PPv @plugin
---

### 説明

PPv は非常に多機能なページャーで ansi エスケープシーケンスにも対応しています。  
つまり、[bat](https://github.com/sharkdp/bat)で構文ハイライトできたりします。  

![sample]({{ site.baseurl }}{% link /public/img/view_bat.png %})

<BR>

ただし bat は utf-8 しか読めませんし、PPv との連携も簡単ではないのでプラグインにしました。  
文字エンコードの変更には nkf を使っています。nkf も bat も処理が重めなので速度面を考えて、
ユーザー指定の拡張子のみ nkf を通す仕組みです。
高性能 PC なら全ての拡張子に nkf を通しても気にならないかもしれません。  

~~問題点として、キャレットモードでは正確なカーソル位置を取得できないので
閲覧用と割り切って使うのがよさそうです。~~  
(※191+2で対応されました。)

### 使い方

ppm-viewをインストールして設定してください。  
標準では以下のキーで構文ハイライトが適用されます。  

| key                   | command                  |
| :-------------------- | :----------------------- |
| (PPc) N               | PPv 起動                 |
| (PPv) CTRL+SHIFT+UP   | 前のファイル             |
| (PPv) CTRL+SHIFT+DOWN | 次のファイル             |
| (PPv) SHIFT+F5        | 構文ハイライト再読み込み |
