---
layout: post
title: エコメニュー
version: 182+4以降
date: 2021-12-26
comment: 投稿。
categories: PPc
---
### 説明
たまにしか使わない複数のメニューをひとつにまとめる設定。<BR>
各メニューを設定ファイルに書き出しておいて、メニューの切り替え項目から読み込む仕組みです。登録キーの節約
ができます。

### 使い方
1. メニューごとに設定ファイルを作っておく。メニュー項目名は`M_UecoSub`にする。設定ファイル名は任意。
1. `M_Ueco`にメニューの切り替え項目を追加。ショートカットキーは`&数字`を推奨。
  - 例`&1:sample = *setcust @sample.cfg`
1. メニューから項目を選択すると、メニュー下部にアイテムが追加される。

こんな感じ。※リアルタイムに変化するわけではありません。

![sample]({{ site.baseurl }}{% link /public/img/eco_menu1.png %})
![sample]({{ site.baseurl }}{% link /public/img/eco_menu2.png %})

#### 設定
```
A_exec = {
cfg  =    ;PPxの設定ファイルをまとめておくディレクトリパス
}

;・本体
; キーに割り当てる
-|M_Ueco =
M_Ueco ={
; &1:TagSys = *setcust @%'cfg'%\zzUecoTagSys.cfg
; &2:DivideMode = *setcust @%'cfg'%\zzUecoDM.cfg
-- =
ex = ??M_UecoSub
}

;・サブメニュー
; 以下を設定ファイルに書き出す
-|M_UecoSub =
M_UecoSub = {
}
```

<BR>
[ファイル振り分けモード]({{ site.baseurl }}{% post_url 19-01-12-mode_divideToDirectory %})のエコメニュー設定例<BR>
※キー変更にスクリプトを使わず、KeyModuleを使っています。

```
M_Ueco = {
&2:DivideMode  = *string o,cback=H263010
                 *ifmatch %so"cback",%*getcust(C_back) %: *stop
                 *setcust @%'cfg'%\zzUecoDM.cfg
                 *setcust S_dividemode:base=%*name(DPN,%FDC)
                 *setcust S_dividemode:Bg=%*getcust(C_back)
                 *setcust S_dividemode:Alst=%*getcust(XC_alst)
                 *setcust S_dividemode:Rclst=%*getcust(X_rclst)
                 *setcust XC_alst = 1,1,0,0,0
                 *setcust X_rclst = 0
                 *customize C_back=%so"cback"
                 *mapkey use,K_dmMap
}
```

<BR>
zzUecoDM.cfg
```
-|M_UecoSub =
M_UecoSub = {
基準パス変更\tCtrl + F5 = *execute ,%*getcust(K_dmMap:^F5)
終了\tEsc     = *execute ,%*getcust(K_dmMap:ESC)
div1に移動\t1 = *execute ,%*getcust(K_dmMap:1)
div2に移動\t2 = *execute ,%*getcust(K_dmMap:2)
div3に移動\t3 = *execute ,%*getcust(K_dmMap:3)
div4に移動\t4 = *execute ,%*getcust(K_dmMap:4)
div5に移動\t5 = *execute ,%*getcust(K_dmMap:5)
}

K_dmMap = {
^F5     , *string o,path=%*input("%*name(DN,"%FDVN")" -title:"select base.." -mode:e -k *editmode -allkey %%: *completelist -history:d)%\
          *setcust S_dividemode:base=%so"path"
          %Oi *ppc -single -noactive %sgo"path" %: *focus
ESC     , *linemessage 振り分け終了
          *setcust XC_alst=%*getcust(S_dividemode:Alst)
          *setcust X_rclst=%*getcust(S_dividemode:Rclst)
          *customize C_back=%*getcust(S_dividemode:Bg)
          *mapkey delete,K_dmMap
          *deletecust "S_dividemode"
          *deletecust "K_dmMap"
          *deletecust "M_UecoSub" ;この行で標準のメニューを読み込んでもいい *setcust @%'cfg'%\zzUecoDefault.cfg
1       , *ppcfile !move,"%*getcust(S_dividemode:base)div1",/min /qstart /nocount /querycreatedirectory:off /log:off
2       , *ppcfile !move,"%*getcust(S_dividemode:base)div2",/min /qstart /nocount /querycreatedirectory:off /log:off
3       , *ppcfile !move,"%*getcust(S_dividemode:base)div3",/min /qstart /nocount /querycreatedirectory:off /log:off
4       , *ppcfile !move,"%*getcust(S_dividemode:base)div4",/min /qstart /nocount /querycreatedirectory:off /log:off
5       , *ppcfile !move,"%*getcust(S_dividemode:base)div5",/min /qstart /nocount /querycreatedirectory:off /log:off
}
```

