---
layout: post
title: リストファイルを使ったメモ
version: PPx181以降
date: 2021-11-06
comment: 仮想リストファイル(::listfile)上で使えるようにした。
categories: PPc
---
### 説明
一行編集でメモを書いて、リストファイルに保存するための設定集。
- 開いているリストファイル上でメモを取れます。
- ~~日付~~ 行に色付けできます。

> - `"`のエスケープ処理に[compCode.js]({{ site.baseurl }}{% post_url 20-12-22-script_compcode %})、リストファイルの保存に[LFsave.js]({{ site.baseurl }}{% post_url 21-01-24-script_lfsave %})を使用。<BR>
> - memo.xlfの一行目には、`;ListFile`の記述が必要。<BR>
> - 実態の存在しないファイル(リンク切れ)はリストファイル上で操作(名前の編集など)できないため、<BR>
    コメント欄にメモを書き込んでいます。[commentSearch.js]({{ site.baseurl }}{% post_url 21-01-09-script_commentSearch %})を使えばコメントの絞り込みができます。<BR>
> - 直接ファイルの読み書きをしているので注意してください。

### 使いかた
 - リストファイルのソート方法を読み込み順にする。
 - `*memo [number]`でメモ用の一行編集を呼び出せる。<BR>
 引数に数字を指定すると行に色を付けられる。<span style="color:green;">1:緑</span>,<span style="color:dimgray;">3:暗灰</span>,<span style="color:darkred;">5:暗赤</span>,<span style="color:red;">8:赤</span>,<span style="color:cyan;">10:水色</span>
 - コメントやマーク、並び順を変更した後は、CTRL+Sで保存。

![sample]({{ site.baseurl }}{% link /public/img/command_memo.gif %})

### 設定

```clean
;エイリアス
A_exec = {
scr  = ;スクリプト親ディレクトリパス
list = ;メモリスト親ディレクトリパス
}

;拡張子判別
E_cr = {
:XLF = C_DIR
}

;PPcキーバインド
KC_main = {
;リストファイル上のエントリ削除用
;D , *if 4==%*js(PPx.Result=PPx.DirectoryType;) %: %K"@^\D" %: *stop

^S , *if 4==%*js(PPx.Result=PPx.DirectoryType;) %: %"リストの保存"%Q"コメント、並び、マーク状態を保存します" %: *script %'scr'%\LFsave.js %: *stop
    %K"@^S"
}

;表示形式
MC_celS = {
メモ(&M) = M F12,5 L cs0 O"_CYA" i"・" O"_WHI" C
}

;ソート
XC_dset = {
listfile = B00000,-1,-1,19,-1,-1,B000000,B00000000000000000000000001,
}

;ユーザーコマンド
_Command = {
MEMO = *script %'scr'%\LFmemo.js,%'list'%\memo.xlf,%*arg(1)
}
```

コンテキストメニュー用<BR>
```clean
メモ(&E)  = *MEMO
赤(&R)    = *MEMO 8
緑(&G)    = *MEMO 1
青(&B)    = *MEMO 10
```

<BR>
<script src="https://gist.github.com/tar80/77243878f3a79d9eca1e2d382c4659a9.js"></script>
