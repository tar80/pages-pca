---
layout: page
title: About
permalink: /about/
---

TORO氏作のファイラ[Paper Plane xUI]()のカスタマイズ記事を投稿しています。<BR>
winXP\_32bit(JScript)とwin10\_64bit(ES6)で動作確認しています。

 - カスタマイズは自己責任でお願いします。また必ずバックアップを取って下さい。<BR>
 `%Odb *cd %0 %: *ppcust CD %0%\PPX_%*nowdatetime(y-N-D).CFG`
 - 全ての記事においてScriptModule(PPXSCR.DLL)R18は入っているものとして扱います。
 - スクリプトはJscriptとES6で記述していますが見える部分の違いはありません。ES6を使う場合は
 ファイル名から`_es6`の部分を除去し、`*setcust _Others:usejs9=4`を実行後
 再起動してください。また文字コードがutf-8の場合はBOMを付けてください。

#### 初期設定
---
カスタマイズで使う全エイリアス。※一時的な登録は除く
```clean
A_exec = {
scr  =    ;スクリプトをまとめておくディレクトリパス
cfg  =    ;PPxの設定ファイルをまとめておくディレクトリパス
list =    ;読み書きするリストをまとめておくディレクトリパス
work =    ;作業用のディレクトリパス
myrepo =  ;gitレポジトリrootパス
reces =   ;reces.exeのパス
}
```

#### PPx本体以外で使っているもの
---
- **PPxModule**
  - MessageModule
  - WindowModule
  - PositionModule
  - TextModule
  - ScriptModule
  - KeyModule
  - EverythingSearchModule

- **外部ソフト**
   - Git for windows [git-scm](https://git-scm.com/download/win) gitコマンド群
   - Fenrir [vector](http://hp.vector.co.jp/authors/VA026310/) fenrirscanのパスリスト生成機能を使っています。
   - Ralpha [nilposoft](http://nilposoft.info/)  画像処理
   - Reces [frostmoon](http://frostmoon.sakura.ne.jp/) 書庫操作
   - Everything [voidtools](https://www.voidtools.com/) インデックス検索
   - Rclone [RCLONE](https://rclone.org/) オンラインストレージ操作

- **外部コマンド**
   - linux command(sed,xargs,grepなど) git for windowsに入っています。
     > よくわからんけどカスタマイズは試してみたいという人はGitForWindowsポータブル版を<BR>
     落としてきて任意のディレクトリに置き、以下の設定をすればPPc上からはパスを通した状態になります。
```clean
KC_main = {
FIRSTEVENT  , *set path+=【任意のディレクトリ】\git\usr\bin
}
```
   - nkf [vector](https://www.vector.co.jp/soft/win95/util/se295331.html)
   - peco [github](https://github.com/peco/peco/releases)
   - ripgrep [github](https://github.com/BurntSushi/ripgrep/releases)
   - jvgrep [github](https://github.com/mattn/jvgrep/releases)
   - colortool [github](https://github.com/microsoft/terminal/releases/tag/1904.29002)

- **外部ライブラリ**
   - migemo [kaoriya](https://www.kaoriya.net/software/cmigemo/) ローマ字検索
   - bregonig [k-takata](http://k-takata.o.oo7.jp/mysoft/bregonig.html) 正規表現
   - 7-zip64.dll,7z.dll [frostmoon](http://frostmoon.sakura.ne.jp/) 統合アーカイバ

---
Powerd by [Jekyll](http://jekyllrb-ja.github.io/).The theme uses [monos](http://jekyllthemes.org/themes/monos/).The icon uses [iconmonstr](https://iconmonstr.com/).Thank you.
