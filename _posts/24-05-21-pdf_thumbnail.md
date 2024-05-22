---
layout: post
title: PDFファイルにサムネイルを設定する
date: 2024-05-21
comment: 投稿。
categories: PPc
---

### 説明

ifextend.spi(Extend convert Susie Plug-in)にPDFのサムネイル用設定を記述します。  
サムネイルの生成には[ghostscript](https://www.ghostscript.com/)を使用するので
あらかじめインストールしておいてください。  
scoopでのインストールが簡単便利です。

#### 設定

`ifextdend.cfg`に以下の設定を追加します。  
サムネイル用に生成速度重視になっているため画像としての解像度は低いです。

```clean
:GS(PDF)
*.pdf
&>"path/to/gswin64c.exe" -dSAFER -dBATCH -dNOPAUSE -sDEVICE=bmp256 -dFirstPage=1 -dLastPage=1 -r50 -sOutputFile="%dst%" "%src%"
```

> `gswin64c.exe`のパスはフルパスを指定。32bit版の実行ファイル名は`gswin32c.exe`です。
