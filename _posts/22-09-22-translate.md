---
layout: post
title: 日本語/英語を相互に翻訳
version: PPx186以降
date: 2022-09-22
comment: 投稿。
repository: tar80/ppm-translate
categories: PPv PPe @plugin
---

### 説明

PPv,PPe,一行編集上からGASを介して日本語/英語の相互翻訳を実行します。  
[こちら](https://www.pnkts.net/2019/12/19/gas-google-transrate)のサイトの翻訳APIをそのままコピーして使わせてもらっていますので、サイトを参考に  
自分でデプロイしたWEBアプリをscript/jscript/translate.js内のURLと置き換えても動作します。  
※長い文章や記号を含む文字列は変換されないことがあります。

### 使い方

ppx-plugin-managerでインストールしてください。  
<BR>
![sample]({{ site.baseurl }}{% link /public/img/translate_via_gas.gif %})

