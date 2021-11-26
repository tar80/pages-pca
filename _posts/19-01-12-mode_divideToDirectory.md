---
layout: post
title: ファイル振り分けモード
version: PPx181以降
date: 2021-11-07
comment: "一時変数を%spから_userに変更"
categories: PPc
---
### 説明
1-5キーを使ってそれぞれdiv1-5ディレクトリにファイルを振り分けるモード設定。
- div1-5ディレクトリはモード起動時のディレクトリ（%'baseDir'）に自動的に作成。
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
| ^F5 | div1-5の親ディレクトリをカレントに変更 |
| ESC  | モード終了。 |

### 設定
```clean
;エイリアス
A_exec = {
cfg  =  ;PPxの設定ファイルをまとめておくディレクトリパス
}

;ユーザーコマンド
_Command = {
&DivideMode = *script %'scr'%\exchangeKeys.js,1,%'cfg'%\zz1DivideMode.cfg
              *setcust _User:dm_baseDir=%*name(DPN,%FDC)
              *setcust _User:dm_Bg=%*getcust(C_back)
              *setcust _User:dm_Alst=%*getcust(XC_alst)
              *setcust _User:dm_Rclst=%*getcust(X_rclst)
              *setcust XC_alst = 1,1,0,0,0
              *setcust X_rclst = 0
              *customize C_back=H263010
              *linemessage [1-5]:%*getcust(_User:dm_baseDir)div1-5に振り分け%bn[^F5]:カレントを基準DIRに変更%bn[ESC]:モード解除
}
```

<BR>
zz1DivideMode.cfg
```clean
KC_main = {
^F5     , *setcust _User:dm_baseDir=%*name(DPN,%FDC)
          *linemessage ParentDir > %*getcust(_User:dm_baseDir)
&^APPS  ,
ESC     , *linemessage 振り分け終了
          *setcust XC_alst=%*getcust(_User:dm_Alst)
          *setcust X_rclst=%*getcust(_User:dm_Rclst)
          *customize C_back =%*getcust(_User:dm_Bg)
          *deletecust _User:dm_baseDir
          *deletecust _User:dm_Bg
          *deletecust _User:dm_Alst
          *deletecust _User:dm_Rclst
          *script %'scr'%\exchangeKeys.js,0,%'cfg'%\zz1DivideMode.cfg
1       , *ppcfile !move,"%*getcust(_User:dm_baseDir)div1",/min /qstart /nocount /querycreatedirectory:off /log:off
2       , *ppcfile !move,"%*getcust(_User:dm_baseDir)div2",/min /qstart /nocount /querycreatedirectory:off /log:off
3       , *ppcfile !move,"%*getcust(_User:dm_baseDir)div3",/min /qstart /nocount /querycreatedirectory:off /log:off
4       , *ppcfile !move,"%*getcust(_User:dm_baseDir)div4",/min /qstart /nocount /querycreatedirectory:off /log:off
5       , *ppcfile !move,"%*getcust(_User:dm_baseDir)div5",/min /qstart /nocount /querycreatedirectory:off /log:off
}
```
