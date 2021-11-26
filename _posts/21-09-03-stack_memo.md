---
layout: post
title: 集積メモ
version: PPx181以降181
date: 2021-09-25
comment: "%OCを%OCPに変更。"
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
> - PPeの起動時にクリップボード操作をしています。また、<BR>
    コマンド内でTextModuleの%*clippedtextを使用しています。

#### 設定
```clean
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
FIRSTEVENT , *ifmatch !0,0%sp"stackmemo" %: %OCP *string o,cb=%*clippedtext %: *cliptext %sp"stackmemo" %: %K"@^V" %: *cliptext %so"cb"
}
```
