---
layout: post
title: 同階層の隣合うディレクトリに移動
version: PPx181以降
date: 2021-11-21
comment: 記事を書いた。
categories: PPc
---
### 説明
親ディレクトリを経由せず、直接隣接するパスに移動するスクリプト。<BR>
元ネタは[5chPPxスレ](http://anago.5ch.net/test/read.cgi/software/1264624581/219)で、[つかさのほえほえ日記](http://hoehoetukasa.blogspot.com/2014/01/ppx_29.html)でも公開されているものをさらに改変しています。<BR>
- 下のようなディレクトリ構成でカレントが`DIR\B`であった場合、引数=`0`で`DIR\A`へ、`1`で `DIR\C`へ移動します。

  ```clean
  DIR
  └┬A
    ├B <-いまここ
    └C
  ```

- 同じ拡張子を持つ仮想ディレクトリへの移動も可能。下の場合、
引数=`0`で`DIR\A.zip`へ、`1`で`DIR\D.zip`へ移動します。

  ```clean
  DIR
  └┬A.zip
    ├A.txt
    ├B.zip <-いまここ
    ├C.7z
    ├D.zip
  ```

- 移動先がない場合はメッセージを表示。`<<Root>>` `<Top>` `<Bottom>`

### 使い方
- `*script %'scr'%\moveDir.js,0` 前のディレクトリへ
- `*script %'scr'%\moveDir.js,1` 後のディレクトリへ
- es6版は移動するパスを一時ファイルに書き出す必要があるので、第二引数で一時ファイルを指定します。
指定がなければ、`%'temp'\ppxcomp.tmp`に書き出します。

<BR>
<script src="https://gist.github.com/tar80/766dd285e12ca745069fc418bfaea3b8.js"></script>
