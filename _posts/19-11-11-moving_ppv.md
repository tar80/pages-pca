---
layout: post
title: カーソル位置に応じてPPvを移動
version: Px181以降
date: 2021-11-20
comment: 左右上下は止めて左右移動のみに変更した。
categories: PPv
---
### 説明
PPc上のカーソル位置が画面真ん中より右にあれば左に、左にあれば右にPPvを移動します。
- 複数枚のPPv起動で停止。%si"vState"が1の時、スクリプトが停止するので除外対象には、
  - PPcからは`*string i,vState=1`を適用。
  - PPvからは`*execute C,*string i,vState=1`を適用。
- 終了時に`*string i,vState=`で%si"vState"を空にする必要があります。

> スクリプト内でPositionModule,WindowModuleを使用。

### 使い方
 - スクリプト内変数`DISP_WIDE`を環境に合わせて設定する。
 - PPvを起動。

※画像は古いカスタム状態です。上下左右移動は廃止しました。
<BR>
![sample]({{ site.baseurl }}{% link /public/img/moving_ppv.gif %})

#### 設定
```clean
;エイリアス
A_exec = {
scr = ;スクリプト親ディレクトリパス
}

;PPvキャレットキーバインド
KV_page = {
LOADEVENT,  *script %'scr'%\movingPPv.js
}

;PPv画像キーバインド
KV_img = {
LOADEVENT,  *script %'scr'%\movingPPv.js
}
```
<BR>
<script src="https://gist.github.com/tar80/8e01a61938a3068501a1fa210e860030.js"></script>
