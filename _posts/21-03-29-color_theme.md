---
layout: post
title: PPvにカラーテーマを適用する
version: PPx181以降
date: 2021-11-20
comment: スクリプトを書き直した。
categories: PPv
---
### 説明
PPvの配色はPPcustから変更するか、CV\_charを直接変更することで設定できます。
しかし色の設定は時間がかかって大変な割にできあがりも微妙だったりするので、
WEB上で公開されているカラーテーマをPPvでも使えるようにしてみました。
> - A\_colorにテーマの色情報が登録されます。
> - テーマディレクトリの存在確認に[result.js]({{ site.baseurl }}{% post_url 20-12-18-script_result %})を使用。
> - メニューの再構築にls, sed, xargsを使用。

### 使い方
1. 最初に[Windows Terminal Themes](https://windowsterminalthemes.dev/)でテーマを探す。
  気に入ったテーマを見つけたら、<BR>
  画面中央下にあるボタン"Get theme"を押下。クリップボードにテーマがコピーされる。<BR>
  ![sample]({{ site.baseurl }}{% link /public/img/colortheme1.png %})
1. %M\_theme > Createを押すと確認のダイアログが出る。了解すると%'cfg'%\theme(初期値)に<BR>
  テーマが生成される。テーマ用のディレクトリがなければ自動で作成される。<BR>
  ![sample]({{ site.baseurl }}{% link /public/img/colortheme2.png %})
3. %M\_themeに2.で生成したテーマが追加され、選択することでテーマが適用される。<BR>
  実行中のPPvへの反映は`%K"@F5`表示を更新する。<BR>
  ![sample]({{ site.baseurl }}{% link /public/img/colortheme3.png %})

4. %M\_theme > Rebuildを押すとテーマメニューが再構築される。メニューの初期化時や、<BR>
不要なテーマを整理した後などに実行。
<BR><BR>
![sample]({{ site.baseurl }}{% link /public/img/colortheme4.png %})

### 設定
- M\_theme
  - テーマ用のメニュー。`MV_menu:&Theme=%M_theme`などに配置する。
  - createColorTheme\.jsの初期設定で定数"THEME\_DIR\_PATH"を変更した場合は
  "&Rebuild"内のパスも変更する。<BR>
- CV\_hkey
  - 配色チェック用。拡張子.colortestの色設定。<BR>
    > この設定は不要であれば省いて構いません。
- createColorTheme\.js
  - 初期設定をする必要あり。詳細はスクリプト内のコメントを参照。
  - PPvだけでなくPPcとPPbにもテーマを適用できる。
  - 初期設定の項目以外にA\_colorにも色が追加される。
- createColorThemePPb\.js
  - PPbにテーマ設定を適用。
  - クリップボードの内容でCB\_palsを変更する。
  - テーマデータは保存されない。

```clean
A_exec = {
scr  =  ;スクリプトをまとめておくディレクトリパス
cfg  =  ;PPxの設定ファイルをまとめておくディレクトリパス
}

; テーマメニュー
M_theme = {
&Create   = *script %'scr'%\createColorTheme.js
&Rebuild  = *deletecust "M_themeSub"
            %OPn *cd %'cfg'%\theme %: *ppb -c ls | sed -r 's/(.*)\.cfg/\*setcust M_themeSub:\1=\*setcust "@%%%%%%%%\'cfg\'%%%%\\\\theme\\\\\1\.cfg" %%%%%%%%: *deletecust M_theme,3 %%%%%%%%: *setcust M_theme:Theme: \1=%%%%%%%%%%%%%%%%M_themeSub %%%%:/' | xargs %0ppbw -c
; 古いsedの場合、上ではエラーが出る。下ならいけるかもしれない。
; &Rebuild  = *deletecust "M_themeSub"
;             %OPn *cd %'cfg'%\theme %: *ppb -c ls | sed -r 's/(.*)\.cfg/\*setcust M_themeSub:\1=\*setcust "@%%%%%%%%\'cfg\'%%%%\\\\theme\\\\\1\.cfg"" %%%%%%%%: *deletecust M_theme,3 %%%%%%%%: *setcust M_theme:Theme: \1=%%%%%%%%%%%%%%%%M_themeSub %%%%:/' | xargs %0ppbw -c
;"
-- =
&Theme    = %M_themeSub
}

; 配色チェック用の設定
CV_hkey = {
COLORTEST = BBLACK,bbla
  BRED,bre
  BGREEN,bgre
  BYELLOW,byel
  BBLUE,bblu
  BPURPLE,bpur
  BCYAN,bcya
  BWHITE,bwhi
  BLACK,black
  RED,red
  GREEN,green
  YELLOW,yellow
  BLUE,blue
  PURPLE,purple
  CYAN,cyan
  WHITE,white
  FG,background
  BG,background
  FG,foreground
  SEL_BG,sel_bg
  CUR,cursor
}
```
<BR>
<script src="https://gist.github.com/tar80/f121348f7b667f69c3f8baa1241e3dd5.js"></script>
