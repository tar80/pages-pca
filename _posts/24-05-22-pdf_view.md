---
layout: post
title: PDFを展開してPPvで閲覧
version: PPx196以降
date: 2024-05-22
comment: 投稿。
categories: PPc PPv
---

### 説明

PDFファイルはPDFビューアで閲覧するものですが、PPvを使ってちょっと見したくなることもあります。  
PPcからPPvを使ってPDFを閲覧するために以下の2つの方法を試しました。

1. [axpdf.sph](http://www.ne.jp/asahi/krk/kct/software/axpdf.htm)を使う。  
   PDFをそこそこの解像度の画像書庫として展開します。
   - 長所: 書庫の展開時間が短い
   - 短所: 解像度の詳細設定ができない
1. [axextend.sph](https://toroidj.github.io/slplugin.html#spextend)に
   [ghostscript](https://www.ghostscript.com/)を設定する。  
   こちらもPDFを画像書庫として展開します。やや複雑。
   - 長所: 解像度の詳細設定ができる
   - 短所: 書庫の展開時間がページ数に比例して長くなる

> axextend.sphはやり方次第で即時に書庫展開する方法があるかも知れませんが
> 思いつきませんでした

どちらも一長一短なので両方の長所を活かした、第3の方法を作りました。  
最初に展開後の画像パスを書き出したリストファイルを開き、同時に別プロセスでghostscriptを  
動作させ逐次展開するしくみです。オプションで展開するページ数に上限を設定できます。

### 設定

ファイル判別(:PDF)に`expandPDF.js`を設定する。  
`*setcust E_cr::PDF,*script path\to\expandPDF.js`

- expandPDF.js,`1`,`2`
  1. `string` 展開するPDFのフルパスを指定(default:`%FDC`)
  2. `number` 展開するページ数の上限を指定(default:`99`)

スクリプト内の`var GS`、`var CMD` は自環境に合わせて調整が必要です。

<script src="https://gist.github.com/tar80/5372f4d5273002144ef676826a5ba2f7.js"></script>
