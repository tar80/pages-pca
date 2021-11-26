---
layout: post
title: "*setentryimageで一括登録したい"
version: PPx181以降
date: 2021-11-06
comment: スクリプトを書き直した。
categories: PPc
---
### 説明
一度の操作で複数のエントリに一括してサムネイルを付ける設定。

### 使い方
 - 元になる画像のパスリストを作っておく。パスは一行に一つずつ書く。
 - スクリプトの第一引数には表示形式(MC_celS)、第二引数には画像リストのパスを指定。
 - サムネイルを設定したいファイルをマークしてコマンドを実行。
 - 一行編集から画像パスを選択。

   ![sample]({{ site.baseurl }}{% link /public/img/set_thumbnail_collect.gif %})

#### 設定
```clean
;エイリアス
A_exec = {
scr = ;スクリプト親ディレクトリパス
}

;コンテキストメニュー
サムネイル一括設定 = *script %'scr'%\setThumbCol.js,【表示形式】,【元画像のパスリスト】
```
<BR>
<script src="https://gist.github.com/tar80/985c2e8a17aec4fbc40af2fb3c9f76e8.js"></script>
