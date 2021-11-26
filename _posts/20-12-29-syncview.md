---
layout: post
title: 窓数に合わせた連動ビューの制御
version: PPx181以降
date: 2021-11-20
comment: コマンドを修正。スクリプトを書き直した。
categories: PPv
---
### 説明
[movinigPPv]({{ site.baseurl }}{% post_url 19-11-11-moving_ppv %})と[behaviorPPv]({{ site.baseurl }}{% post_url 20-05-02-behavior_ppv %})を使い、PPcの窓数に応じて連動ビューの表示方法を変更する設定集。<BR>
連動ビュー起動時にペインが一枚であれば普通に起動。反対窓があればcupturewindowに取り込み。
その際、取り込む前のサイズを記憶しておいて連動ビュー終了時に元のサイズに戻します。

> - 一体型横分割のPPc以外でどのような挙動をするかは未確認。
> - コマンド内で%\*midstr(PPXTEXT.DLL)を使用。
> - 常に%si"vState"を意識しておかないとPPvが予想しない位置に飛んでいくので注意が必要。
> - PPvの一行編集には以下を仕込んで置かないとコマンド実行時にLOADCUSTされ、意図せぬ設定変更が適用されてしまいます。

  ```clean
  key, *linecust Do,KV_img:ACTIVEEVENT,
       *linecust Do,KV_main:ACTIVEEVENT,
  ```

### 使い方
 - `Y`キーを押下。

#### キーバインド
**PPc**

| ACTION | COMMAND |
|:-:|:-|
| Y | 連動ビュートグル |

**PPv**

| ACTION | COMMAND |
|:-:|:-|
| Y | 連動ビュートグル |
| : | 一行編集 |
| SPACE | カーソル行マーク |

#### 設定
```clean
;エイリアス
A_exec = {
cfg = ;設定ファイルのあるディレクトリパス
scr = ;スクリプト親ディレクトリパス
}

;ユーザーコマンド
_Command = {
; 連動ビューの起動状態に合わせて挙動を変える。引数は、UP DOWN LEFT RIGHT SPACEのいずれか
PPVUD = *if 0%*extract(C,"%%*js(PPx.Result=PPx.SyncView;)") %: %K-C"@%*arg(1)" %: *stop
        *execute C,*ppvoption id %*midstr(%n,1) %: %K-C"@%*arg(1)@N"
}

;PPcキーバインド
KC_main = {
Y , *script %'scr'%\syncview.js
}

;PPvキーバインド
KV_main = {
LOADEVENT   , *customize XV_drag=0,1,3,2        ;ドラッグの設定
              *setcust MV_click:L_SPC=@^ENTER   ;左クリック(コンテキストメニュー)
              *setcust @%'cfg'%\zz2HMppvDoc.cfg ;隠しメニュー設定ファイル
              *linecust Do,KV_main:ACTIVEEVENT,%%K"@LOADCUST"
              *if !0%sp"vState" %: *script %'scr'%\movingPPv.js
ACTIVEEVENT , *setcust MV_click:L_SPC=@^ENTER
              *setcust XV_drag=0,1,3,2
              *setcust @%'cfg'%\zz2HMppvDoc.cfg
CLOSEEVENT  , *setcust X_bg:O_%n=100
              *zoom -2
^DOWN       , *PPVUD DOWN
^UP         , *PPVUD UP
':'         , *linecust Do,KV_img:ACTIVEEVENT,
              *linecust Do,KV_main:ACTIVEEVENT,
              *execute %n,%*input(-title:"Execute.." -mode:h)
Y           , *execute C,*script %'scr'%\syncview.js
}

;PPv画像キーバインド
KV_img = {
LOADEVENT   , *setcust MV_click:L_SPC=          ;左クリック(何もしない)
              *setcust XV_tmod=0                ;キャレットモードを解除
              *setcust _User:u_rotate_PPvBG=1
              *setcust @%'cfg'\zz2HMppvImg.cfg
              *customize XV_drag=1,0,3,2
              *script %'scr'%\movingPPv.js
              *linecust Do,KV_img:ACTIVEEVENT,%%K"@LOADCUST"
ACTIVEEVENT , *setcust MV_click:L_SPC=
              *setcust XV_drag=1,0,3,2
              *setcust @%'cfg'%\zz2HMppvImg.cfg
DOWN        , *PPVUD DOWN
LEFT        , *PPVUD LEFT
RIGHT       , *PPVUD RIGHT
UP          , *PPVUD UP
SPACE       , *PPVUD SPACE
}
```
<BR>
<script src="https://gist.github.com/tar80/aa90167da7e935a913c24cf5fd0ec471.js"></script>
