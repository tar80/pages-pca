---
layout: post
title: PPvの挙動を動的に変更
version: PPx181以降
date: 2021-11-20
comment: スクリプトを書き直した。
repository: tar80/ppm-view
categories: PPv
---
### 説明
PPvで開いたファイルの種類に合わせて画像・テキスト表示時の左クリック・ドラッグ・隠しメニューをそれぞれ変更するための設定集。
隠しメニューの変更には別途書き出した設定ファイルを使用します。

> - ユーザーコマンド内でPPXTEXT.DLL、隠しメニュー内でPPXWIN.DLLを使用。
> - ACTIVEEVENTで随時表示を更新しているので設定によっては弊害がでる可能性があります。<BR>
  その際は問題のでるコマンドに以下を追記。
> ```clean
  key , *linecust Do,KV_main:ACTIVEEVENT,
         *linecust Do,KV_img:ACTIVEEVENT,
  ```

### 使い方
- PPvを起動しファイルを開く。
- 別のファイルを開くと自動で設定が変更される。

[苺のアップ画像が表示されます。ブツブツ嫌いな人は要注意]({{ site.baseurl }}{% link /public/img/behavior_ppv.gif %})

#### マウスジェスチャー
**PPv**

| ACTION        | COMMAND     |
|:-|:-|
| 右クリック + ↑ | 前のファイル |
| 右クリック + ↓ | 次のファイル |

#### 設定
```clean
;エイリアス
A_exec = {
cfg = ;設定ファイルのあるディレクトリパス 例)%0\cfg
scr = ;スクリプト親ディレクトリパス
}

;ユーザーコマンド
_Command = {
; 連動ビューの起動状態に合わせて挙動を変える。引数は、UP DOWN LEFT RIGHTのいずれか
PPVUD = *if %*extract(C,"%%*js(PPx.Result=PPx.SyncView;)") > 0 %: %K-C"@%*arg(1)" %: *stop
        *execute C,*ppvoption id %*midstr(%n,1) %: %K-C"@%*arg(1)@N"
}

;PPvキーバインド
KV_main = {
LOADEVENT   , *customize XV_drag=0,1,3,2        ;ドラッグの設定
              *setcust MV_click:L_SPC=@^ENTER   ;左クリック(コンテキストメニュー)
              *setcust @%'cfg'%\zz2HMppvDoc.cfg ;隠しメニュー設定ファイル
              *linecust Do,KV_main:ACTIVEEVENT,%%K"@LOADCUST"
ACTIVEEVENT , *setcust MV_click:L_SPC=@^ENTER
              *setcust @%'cfg'%\zz2HMppvDoc.cfg
              *setcust XV_drag=0,1,3,2
}

;PPv画像キーバインド
KV_img = {
LOADEVENT   , *setcust MV_click:L_SPC=          ;左クリック(何もしない)
              *setcust XV_tmod=0                ;キャレットモードを解除
              *setcust _User:u_rotate_PPvBG=1
              *setcust @%'cfg'\zz2HMppvImg.cfg
              *customize XV_drag=1,0,3,2
              *linecust Do,KV_img:ACTIVEEVENT,%%K"@LOADCUST"
ACTIVEEVENT , *setcust MV_click:L_SPC=
              *setcust XV_drag=1,0,3,2
              *setcust @%'cfg'%\cfg\zz2HMppvImg.cfg
}

;PPv_コンテキストメニュー
M_ppvc = { ; PPvコンテキスト追加分
背景明度(&B) = *string o,bri=X_bg:B_%n
               *customize %so"bri"=%*input("%*getcust(%so"bri")" -title:"明度の設定" -mode:n)
}

;PPv_マウスジェスチャー
MV_click = {
RG_U , *PPVUD UP
RG_D , *PPVUD DOWN
}
```
<BR>
<script src="https://gist.github.com/tar80/ae59cd088f97f507fb0217bf42828351.js"></script>
