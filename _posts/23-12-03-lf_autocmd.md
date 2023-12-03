---
layout: post
title: リストファイル個別のコマンド実行
version: PPx194, ppm0.93.7以降
date: 2023-12-03
comment: 投稿。
repository: tar80/ppx-plugin-manager
categories: PPc @plugin
---

### 説明

リストファイルは様々な用途に利用でき、また表示内容も大きく異なるのでリストごとに表示形式を設定したくなります。
このような場合、ディレクトリ別設定(XC_dset)を使ってパスごとに表示方法を設定できますが、
リストファイルごとの設定が増えればXC_dsetの内容は肥大し管理も面倒になります。  
そこで、リストファイルに直接"cmd:"の内容を書き込んで、開くときに参照する方法を考えました。  
これなら使わなくなったリストファイルの設定がXC_dsetに残ることもなく管理の手間も係りません。やったね。

### 使い方

リストファイルを開く。  
一つ注意点として、開いても表示が変更されないときはファイルを読み取れていない可能性があります。  
PPxの標準的な設定ではリストファイルのエンコードは"utf16le"になっている場合が多いでしょうが、
ppmの標準では"utf8"としてファイルを扱うため、それ以外のエンコードは開けないことがあります。  
対策としては、下の設定で使われている`lfMetadata.js`の第3引数に`utf16le`を指定する方法と、
リストファイルのエンコードをutf8に変更する方法があります。いずれにしてもリストファイルのエンコードは
揃えておくと問題が起こりにくいです。ppmを利用するならutf8に統一することを推奨します。

#### 設定

1. "XC_dset:listfile"の"*diroption"に  
   `cmd:"*execute ,%*script(""%sgu'ppmlib'\lfMetadata.js"",%FDV,cmd,utf8)"`を設定する
1. リストファイルのメタデータに`;cmd=<設定内容>`を記述する。ファイルのエンコードにも注意する。

メタデータの記述例:  
表示形式を変更 `;cmd=*viewstyle -temp format "<formats>"`  
背景画像を変更 `;cmd=*setcust X_bg:P_%n=<background.jpg> %: *setcust X_bg:T_%n=20 %: *color back`  

> 背景画像を変更する場合、`XC_dset:*`に戻す処理を設定する必要があります。  
> `cmd:"*deletecust X_bg:P_%n %: *deletecust X_bg:T_%n %: *color back"`

