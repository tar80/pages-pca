---
layout: post
title: ファイル振り分けモード
version: PPx181以降
date: 2021-12-22
comment: "一時変数を\_userからS\_dividemodeに変更。ctrl+F5キーを調整"
repository: tar80/ppm-switchmenu
categories: PPc @plugin
---
### 説明
1-5キーを使ってそれぞれdiv1-5ディレクトリにファイルを振り分けるモード設定。
- div1-5ディレクトリはモード起動時のディレクトリ（S\_dividemode:base）に自動的に作成。
- モード終了時に使用キーは元の設定に戻ります。

> - %'cfg'にzz1DivideMode.cfgを配置する必要があります。
> - キー設定の変更に[exchangeKeys.js]({{ site.baseurl }}{% post_url 21-03-09-script_exchangeKeys %})を使っています。<BR>
> key moduleを導入している場合は、該当箇所を\*mapkeyで置き換えればスクリプトは不要です。


### 使いかた
- ユーザーコマンド`*DivideMode`で起動。
- 1-5キーを押下してファイルを振り分ける。
- ESCで終了。

### キーバインド
**PPc**

| KEY | COMMAND          |
|:---:|:-----------------|
| 1 | ディレクトリ'div1'にファイルを送る。 |
| 2 | ディレクトリ'div2'にファイルを送る。 |
| 3 | ディレクトリ'div3'にファイルを送る。 |
| 4 | ディレクトリ'div4'にファイルを送る。 |
| 5 | ディレクトリ'div5'にファイルを送る。 |
| ^F5 | div1-5の親ディレクトリをカレントディレクトリに変更 |
| ESC  | モード終了。 |

### 設定
```
;エイリアス
A_exec = {
cfg  =  ;PPxの設定ファイルをまとめておくディレクトリパス
}

;ユーザーコマンド
_Command = {
DivideMode = *script %'scr'%\exchangeKeys.js,1,%'cfg'%\zz1DivideMode.cfg
             *setcust S_dividemode:base=%*name(DPN,%FDC)
             *setcust S_dividemode:Bg=%*getcust(C_back)
             *setcust S_dividemode:Alst=%*getcust(XC_alst)
             *setcust S_dividemode:Rclst=%*getcust(X_rclst)
             *setcust XC_alst = 1,1,0,0,0
             *setcust X_rclst = 0
             *customize C_back=H263010
             *linemessage [1-5]%*getcust(S_dividemode:base)div1-5に振り分け%bn[^F5]カレントを基準DIRに変更%bn[ESC]モード解除
}
```

<BR>
zz1DivideMode.cfg
```
KC_main = {
^F5     , *string o,path=%*input("%*name(DN,"%FDVN")" -title:"select base.." -mode:e -k *editmode -allkey %%: *completelist -history:d)%\
          *setcust S_dividemode:base=%so"path"
          %Oi *ppc -single -noactive %sgo"path" %: *focus
ESC     , *linemessage 振り分け終了
          *setcust XC_alst=%*getcust(S_dividemode:Alst)
          *setcust X_rclst=%*getcust(S_dividemode:Rclst)
          *customize C_back=%*getcust(S_dividemode:Bg)
          *deletecust "S_dividemode"
          *script %'scr'%\exchangeKeys.js,0,%'cfg'%\zz1DivideMode.cfg
1       , *ppcfile !move,"%*getcust(S_dividemode:base)div1",/min /qstart /nocount /querycreatedirectory:off /log:off
2       , *ppcfile !move,"%*getcust(S_dividemode:base)div2",/min /qstart /nocount /querycreatedirectory:off /log:off
3       , *ppcfile !move,"%*getcust(S_dividemode:base)div3",/min /qstart /nocount /querycreatedirectory:off /log:off
4       , *ppcfile !move,"%*getcust(S_dividemode:base)div4",/min /qstart /nocount /querycreatedirectory:off /log:off
5       , *ppcfile !move,"%*getcust(S_dividemode:base)div5",/min /qstart /nocount /querycreatedirectory:off /log:off
}
```
