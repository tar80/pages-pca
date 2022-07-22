---
layout: post
title: viキーバインド
version: PPx185+6以降
date: 2022-07-22
comment: 投稿。
repository: tar80/ppm-vilikekeys
categories: PPc @plugin
---
### 説明

PPcの操作に及ぼす影響を押さえつつviのキーバインドを再現します。  
viそのままのキーバインドでは使い辛いものは独自に変更してありますが、
利用者側で変更可能です。  
`h`,`j`,`k`,`l`,`/`,`:`に加え、オペレーター`g`,`q`が使えます。オペレーターgではカウントが使えます。  

### 使い方

ppmのプラグインリストに`remote 'tar80/ppm-vilikekeys'`を加え、`*ppmInstall`してください。  

`/`キーのインクリメンタルサーチでは`SPACE`でマークできる他、`CTRL+N`、`CTRL+P`で
カーソル移動できます。

オペレーターgではカウントが使えます。`g100j`でカーソルを100下に移動し、`g10l`とすれば
10ページ先に移動します。

<BR>
![sample]({{ site.baseurl }}{% link /public/img/vi_like_keys.gif %})

