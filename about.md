---
layout: page
title: About
permalink: /about/
---

TORO氏作のファイラ[Paper Plane xUI](http://toro.d.dooo.jp/slppx.html)のカスタマイズ記事を投稿しています。  
winXP\_32bit(JScript)とwin10\_64bit(ES6)で動作確認しています。

- カスタマイズは自己責任でお願いします。また必ずバックアップを取って下さい。  
`%Odb *cd %0 %: *ppcust CD %0%\PPX_%*nowdatetime(y-N-D).CFG`
- 全ての記事においてScriptModule(PPXSCR.DLL)R18は入っているものとして扱います。
- スクリプトはJScriptとES6で記述していますが見える部分の違いはありません。ES6を使う場合は
ファイル名から`_es6`の部分を除去し、`*setcust _Others:usejs9=4`を実行後
再起動してください。また文字コードがutf-8の場合はBOMを付けてください。[スクリプトのダウンロードヘルパー]({{ site.baseurl }}{% post_url 21-11-28-gist_download_helper %})も参考にしてください。
- プラグイン形式でのカスタマイズ公開に向けて[ppx-plugin-manager](https://github.com/tar80/ppm)を作成中。  
現在ベータテスト版を公開しています。

#### 初期設定

カスタマイズで使う全エイリアス。※一時的な登録は除く

> これらのエイリアスはppx-plugin-managerでは使いません。

<BR>
```text
A_exec = {
scr  =    ;スクリプトをまとめておくディレクトリパス
cfg  =    ;PPxの設定ファイルをまとめておくディレクトリパス
list =    ;読み書きするリストをまとめておくディレクトリパス
work =    ;作業用のディレクトリパス
myrepo =  ;gitレポジトリrootパス
reces =   ;reces.exeのパス
gvim =    ;gvim.exeのパス
}
```

####  PPx本体以外で使っているもの

- **PPxModule**
  -  MessageModule
  -  WindowModule
  -  PositionModule
  -  TextModule
  - ScriptModule
  - KeyModule
  - EverythingSearchModule

- **外部ソフトウェア**
  - Git for windows [git-scm](https://git-scm.com/download/win) バージョン管理システム
  - Fenrir [vector](http://hp.vector.co.jp/authors/VA026310/) fenrirscanのパスリスト生成機能を使っています。
  - Ralpha [nilposoft](http://nilposoft.info/)  画像処理
  - Reces [frostmoon](http://frostmoon.sakura.ne.jp/) 書庫操作
  - Everything [voidtools](https://www.voidtools.com/) インデックス検索
  - Rclone [RCLONE](https://rclone.org/) オンラインストレージ操作
  - Windows Terminal [github](https://github.com/microsoft/terminal/releases) 端末エミュレータ
  - Nyagos [github](https://github.com/nyaosorg/nyagos/releases) コマンドラインシェル

- **外部コマンド**
  - linux command(sed,xargs,grepなど) git for windowsに入っています。
    > よくわからんけどカスタマイズは試してみたいという人はGitForWindowsポータブル版を
    落としてきて任意のディレクトリに置き、以下の設定をすればPPc上からはパスを通した状態になります。

    ```text
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

<BR>

---
Powerd by [Jekyll](http://jekyllrb-ja.github.io/).The theme forked [monos](http://jekyllthemes.org/themes/monos/).The icons from [iconmonstr](https://iconmonstr.com/).Thank you.
