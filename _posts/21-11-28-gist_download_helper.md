---
layout: post
title: スクリプトのダウンロードヘルパー
version: PPxScriptModuleR18以降
date: 2021-11-28
comment: 投稿。
categories: PPc Script
---
### 説明
このサイトに埋め込まれているgistのPPxScriptModule用コードを、ファイル名から`_es6`を除去し
utf8-bomに変換した上で%'scr'ディレクトリにダウンロードするユーザーコマンドです。

> コマンド内でPPXTEXT.DLL(%\*clipedtext)及びcurl,nkfを使っています。※curlはgit for windowsに同梱されています。

### 使い方
1. 記事内gist埋め込みコードの右下にある"view raw"にカーソルを合わせ、右クリックから"リンクアドレスをコピー"する。
1. PPc一行編集から`*dlgist`を実行。ダイアログが出るので内容を確認してOK。
1. 成功すれば%'scr'にスクリプトが作成される。ファイル名が重複していた場合は数字を付加。

### 設定
```
A_exec = {
scr  =    ;スクリプトをまとめておくディレクトリパス
}

_Command = {
dlgist = *string o,url=%*clippedtext()
         *string o,name=%*regexp("%so"url"","s/^.*\/(.*\.js)$/$1/")
         *ifmatch !.js,%so"name" %: %"処理の中止"%I"スクリプトではありません%bn%so"name"" %: *stop
         %"curl"%Q%so"name"をダウンロードします
         *string o,filepath=%'scr'%\%*regexp("%so"name"","s/_es6//")
         %On *ppb -c curl -L "%so"url"" | nkf -w8 -Lu > "%*name(U,%so"filepath")"
}
```

