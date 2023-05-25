---
layout: post
title: 状況に合わせて右クリックメニューを表示
version: PPx1818以降
date: 2021-11-06
comment: sux用メニューを追加した。
categories: PPc
---

### 説明

現在のディレクトリ>カーソルエントリ拡張子に応じてメニューを作成するスクリプト。

> - コマンド内(コメントアウト)で[result.js]({{ site.baseurl }}{% post_url 20-12-18-script_result %})、[7-zip64.dll]({{ site.baseurl }}http://frostmoon.sakura.ne.jp/7-zip32_ungarbled/)、[Reces]({{ site.baseurl }}http://frostmoon.sakura.ne.jp/reces/)、[Ralpha]({{ site.baseurl }}http://nilposoft.info/)を使っています。
> - 文字操作に nkf,sed を使用。

### 使いかた

- メニュー項目を好きなようにいじくる。
- スクリプトの初期設定をする。
- 右クリック。

### 設定

```clean
;エイリアス
A_exec = {
scr = ;スクリプト親ディレクトリパス
work = ;作業用ディレクトリ
;reces = ;reces.exeのパス
}

;PPcキーバインド
KC_main = {
APPS , *script %'scr'%\keyApps.js
}

;PPcクリック
MC_click = {
R_ENT , *script %'scr'%\keyApps.js
}

;ポップアップメニュー
;共通部分
-|M_Ccr =
M_Ccr   = {     ; PPc ^[Enter] ContextMenuへの追加分
Ext = ; タイプ別メニュー本体。この項目は消したらダメ。
%G"JMCP|Copy"(&C)       = %K"@C"
%G"JMMO|Move"(&M)       = %K"@M"
%G"JMAE|Unpack"(&U)     = %K"@U"
%G"JMRU|Execute"(&X)    = %K"@X"
%G"JMTE|Text Edit"(&E)  = %"JMTE|Text edit"%Orib,editor %FDC
PP&v    = %K"@N"
%G"JMPV|Preview"(&I)    = *entrytip preview
--      =
%G"JMCU|Cut"(&T)        = %K"@^X"
%G"JMCL|Clip"(&L)       = %K"@^C"
%G"JMPA|Paste"(&P)      = %K"@^V"
--      =
%G"PJRB|To Recycle bin"(&D)     = %K"@D"
%G"JMDE|Delete"(&Q)     = %K"\@D"
%G"JMRE|Rename"(&A)     = %K"@R"
--      =
%G"JMPR|Properties"(&R) = %z,properties
}

;type_anon
-|M_Uanon =
M_Uanon = {
移動形式 = ??exjumpmenu
-- =
}

;type_dir
-|M_Udir =
M_Udir  = {
ディレクトリ内検索(&W) = *where -path:"%#;FC"
--  =
画像変換(&R) =?M_Uimg1
--  =
}

;type_img
-|M_Uimg =
M_Uimg = {
移動形式        = ??exjumpmenu
-- =
背景に設定(&B)  = *setcust X_bg:P_%n=%FDC
                  *setcust X_bg:T_%n=20
                  *color back
-- =
画像変換(&R)    = ??M_Uimg1
-- =
}

;type_img_sub
-|M_Uimg1 =
M_Uimg1 = {
;&Ralpha     = %Oi *launch %'ralpha'
;              *autodragdrop "Ralpha Image Resizer",%FDC
;&RalphaCMD  = *string o,inipath=%*name(C,"%*input("c:\bin\ralpha\ini\" -title:"設定ファイルの選択" -mode:g -select:l -k:*completelist)")
;              ralpha /ini=%so"inipath" %#FDC
}

;type_doc
-|M_Udoc =
M_Udoc = {
;文字列置換(&R)  = *if 4==%*js(PPx.Result=PPx.DirectoryType;) %: *script %'scr'%\LFexec.js,sed,1 %: *stop
;                  %On *ppb -c sed -i_back -r "s#%*script(%'scr'%\compcode.js,"iX","""%%","[検索文字#置換文字] ※\=\\\\")#g" %#FDC
;-- =
;文字コード変換: &SJIS(CRLF)    = %On *PPb -c nkf -s -Lw -c -x --no-best-fit-chars --overwrite %#FDC
;文字コード変換: &UTF16LE       = %On *PPb -c nkf -w16L -x --no-best-fit-chars --overwrite %#FDC
;文字コード変換: UTF&8(LF)      = %On *PPb -c nkf -w -Lu -d --overwrite %#FDC
;文字コード変換: UTF8&BOM(LF)   = %On *PPb -c nkf -w8 -Lu -d --overwrite %#FDC
}

;type_arc
-|M_Uarc  =
M_Uarc = {
書庫内検索(&W)  = *where -path:"%R" -vfs
-- =
移動形式(&Y)    = ??exjumpmenu
-- =
解凍(&U)        =?M_xunpack
}

;type_arc_sub
-|M_xunpack =
M_xunpack = {
ここに解凍(&U)      = *unpack
;--  =
;ワークスペース(&W)  = *unpack %'work'
;反対窓に解凍(&O)    = *ifmatch "64;96",%*RESULT(DirectoryType) %: %u7-zip64.dll,e %FD -i%@ -aos "-o%~FDN" %: *stop
;                      %u7-zip64.dll,x -aos -an -ai%@ "-o%~FDN"
;--  =
;画像抽出(&P)        = %u7-zip64.dll,e -aos -an -ai%@ "-o%*RESULT(getpath)*" *.jpg *.jpeg *.bmp *.png *.gif
;音楽抽出(&M)        = %u7-zip64.dll,e -aos -an -ai%@ "-o%*RESULT(getpath)*" *.mp3 *.mid *.wav *.ogg
;--  =
; 完全性テスト(&T)   = %u7-zip64.dll,t %R
}

;書庫ポップアップメニュー
-|M_Carc  =
M_Carc  = {
下層を開く(&O)  = %K"C_DIR"
;-- =
;ワークスペースに解凍(&U)    = *unpack %'work'
;ディレクトリにまとめる(&U)  = *unpack %*RESULT(getpath)UNPACK
;--  =
;名前の変更(&R)      = %On *ppb -c reces /mn%C:%*input("%*regexp("%FC","s/(.*\\)(.*)/$1|$2|/")" -title:"書庫内ファイル名の変更" -mode:Ec -select:i) %FD %&%K"@^F5"
;移動(&M)        = *script %'scr'%\cmdMove.js
;コピー(&C)      = *script %'scr'%\cmdCopy.js,0
;削除(&D)        = %"ファイル操作" %Q"書庫から削除します" %: %u7-zip64.dll,d -hide %FD %@
}

;aux:パス
-|M_Caux =
M_Caux = {
;コピー(&C)  = *script %'scr'%\cmdCopy.js,1
;移動(&M)    = *script %'scr'%\cmdMove.js
削除(&D)    = %K"@\D"
}

;リストファイルポップアップメニュー
-|M_Clist =
M_Clist = {
該当ディレクトリ(&J)  = *jumppath %FC -entry
-- =
Ext = ; タイプ別メニュー本体。この項目は消したらダメ。
リストから消去(&D)    = %K"@^\D"
;--  =
;メモ(&E) = *MEMO
;赤(&R)   = *MEMO 8
;緑(&G)   = *MEMO 1
;青(&B)   = *MEMO 10
;-- =
;実体削除(&D)    = *ifmatch /^aux:.*/,%FD %: %K"@\D %: *stop
;                  %"ファイル操作" %Q"選択エントリを削除します"
;                  %Oa *ppcfile !safedelete,%*name(HP,%1),-min -qstart -compcmd %K"@^\D"
}

;HTTPポップアップメニュー
-|M_Chttp =
M_Chttp = {
}
```

<BR>
<script src="https://gist.github.com/tar80/d0cd51d9dcb174d01a326ad5f2ec85f4.js"></script>
