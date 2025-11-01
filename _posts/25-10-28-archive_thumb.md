---
layout: post
title: 画像書庫にサムネイルを適用する
version: PPx205以降
date: 2025-11-1
comment: 使用するコマンドに7zが抜けていたので追加した
repository: tar80/ppm-misc
categories: PPc @plugin
---

### 説明

ifextend.spiを使って書庫内の最初の画像ファイルをサムネイルに登録します。  
コマンド内で、7z, nkf, sed, xargs, magick を使用します。 重い処理なので、頻繁に表示する書庫はサムネイルキャッシュ化推奨です。

### 使い方

ifextend.cfgに式を追加し、画像を含む書庫のあるディレクトリでサムネイルを表示します。注意点として、`X_wsiz`(ファイルの読み込みで警告する大きさ)の値を書庫より大きくしておく必要があります。

#### 設定

- ifextend.cfg

```
:Archive
*.zip
&cmd /c 7z l "%src%" | nkf -w | sed -nr "/\.(jpg|png|bmp|gif|webp)$/{s/^.*\s(\S*)$/\1/;s/\\\/\//g;p;q}" | xargs -I {} 7z e "%src%" "{}" -so | magick -[0] -resize "x1000>" "%dst%"
```

- complist\thumbcmd.txt  
  ppm-miscの`setThumbnail.stay.js`からもサムネイルキャッシュを設定できます。

```
%m"arch" <zip> %OP 7z l "%src%" | nkf -w | sed -nr "/\.(jpg|png|bmp|gif|webp)$/{s/^.*\s(\S*)$/\1/;s/\\\/\//g;p;q}" | xargs -I {} 7z e "%src%" "{}" -so | magick -[0] -resize "x1000>" "%dst%"
```

> 参照するビットマップのサイズが大きすぎるとエラーになることがあったので リサイズ処理は入れておいた方が安定するでしょう。

