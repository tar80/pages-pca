---
layout: post
title: 集積メモ
version: PPx182+3以降
date: 2021-12-26
comment: クリップボードの利用をやめた。
categories: PPc
---
### 説明
作業中にメモを取り貯めて後でまとめてPPeに表示する設定集。

### 使い方
1. PPc、ppv上でメモを取る。
2. PPeを起動すると行頭にメモが表示される。

![sample]({{ site.baseurl }}{% link /public/img/stack_memo.gif %})

> - メモは%sp"stackmemo"に入っています。<BR>
  削除はPPcを終了。または`*string p,stackmemo=`を実行。
> - PPv上からPPeを起動する場合は`*execute C,*ppe`とする。

#### 設定
```
;PPc用コマンド
key or menu , %OCP *string p,stackmemo=## %*input(-title:"stack memo.." -mode:e)%bn%sp"stackmemo"

;PPv用コマンド
key or menu , *string o,sm=## %*input(-title:"stack memo.." -mode:e)
              %OCP *execute C,*string p,stackmemo=%so"sm"%%bn%%sp"stackmemo"

;PPvコンテキストメニュー(選択範囲用)
M_ppvc  = {
stack &memo = %OCP *execute C,*string p,stackmemo=## %*selecttext%%bn%%sp"stackmemo"
}

;PPe
K_ppe = {
FIRSTEVENT , *ifmatch !0,0%sp"stackmemo" %: *insert %sp"stackmemo"
}
```
