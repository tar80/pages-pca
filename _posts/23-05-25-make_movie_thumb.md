---
layout: post
title: 動画サムネイルの作成
version: PPx191以降
date: 2023-05-25
comment: 投稿。
repository: 
categories: PPc
---

### 説明

ifextend.spi(Extend convert Susie Plug-in)を導入するとPPcで動画のサムネイルを生成できるようになります。  
デフォルトではffmpegを使用しファイル頭から2秒後の画像を抽出するようになっていますが、ファイルによっては  
内容がよくわからないサムネイルができることもあると思います。  
そこで動画サムネイルを手動で設定するコマンドを用意しました。  

> コマンド内でffmpegを使用

### 使い方

メニュー、キーなどに以下のマクロを設定し実行します。  

```clean
*string o,sec=%*input("2" -title:"サムネイルを抽出する開始秒を指定" -mode:e)
*string o,path=%*temp()thumb.bmp
%Obds ffmpeg -y -ss %so'sec' -i %R -vf thumbnail,scale=900:-1 -frames:v 1 %so'path'
*setentryimage %so'path' -save
*zoom +0
```

1. コマンドを実行すると一行編集が表示され、サムネイルの抽出を開始する秒を指定できます。  
1. 指定した秒から100フレームが解析され、サムネイルに適した画像が抽出されます。  

> - 常にカーソル下のファイルに対して実行され、マークは無視します。
> - ffmpegのパスが通っていない場合はフルパスを指定する必要があります。
