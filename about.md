---
layout: page
title: About
permalink: /about/
---

TORO 氏作のファイラ[Paper Plane xUI](http://toro.d.dooo.jp/slppx.html)のカスタマイズ記事を投稿しています。  
winXP_32bit(JScript5.7)と win10_64bit(ECMAScript6)で動作確認しています。

- カスタマイズは自己責任でお願いします。また必ずバックアップを取って下さい。  
  `*ppcust CD %0%\PPX_%*now(date).CFG`
- 全ての記事において ScriptModule(PPXSCR.DLL)R19 は導入済みとして扱われます。
- スクリプトは JScript と ES6 で記述していますが見える部分の違いはありません。ES6 を使う場合は
  ファイル名から`_es6`の部分を除去し、`*setcust _Others:usejs9=4`を実行後
  再起動してください。また文字コードが utf-8 の場合は BOM を付けてください。
  [スクリプトのダウンロードヘルパー]({{ site.baseurl }}{% post_url 21-11-28-gist_download_helper %})も参考にしてください。
- プラグイン形式でのカスタマイズ公開に向けて[ppx-plugin-manager](https://github.com/tar80/ppm)を作成中。  
  現在ベータテスト版を公開しています。

#### 初期設定

カスタマイズで使う全エイリアス。※一時的な登録は除く

> これらのエイリアスは ppx-plugin-manager では使いません。

```text
A_exec = {
scr  =    ;スクリプトをまとめておくディレクトリパス
cfg  =    ;PPxの設定ファイルをまとめておくディレクトリパス
list =    ;読み書きするリストをまとめておくディレクトリパス
work =    ;作業用のディレクトリパス
myrepo =  ;gitリポジトリrootパス
reces =   ;reces.exeのパス
gvim =    ;gvim.exeのパス
}
```

#### PPx 本体以外で使っているもの

- **PPxModule**

  - EverythingSearchModule
  - KeyModule
  - MessageModule
  - PositionModule
  - ScriptModule
  - TextModule
  - WindowModule

- **外部ソフトウェア**

  - Everything [voidtools](https://www.voidtools.com/) インデックス検索
  - Fenrir [vector](http://hp.vector.co.jp/authors/VA026310/) fenrirscan のパスリスト生成機能を使っています。
  - Git for windows [git-scm](https://git-scm.com/download/win) バージョン管理システム
  - Mpv [mpv.io](https://mpv.io/) メディアプレイヤー
  - Nyagos [github](https://github.com/nyaosorg/nyagos/releases) コマンドラインシェル
  - Ralpha [nilposoft](http://nilposoft.info/) 画像処理
  - Windows Terminal [github](https://github.com/microsoft/terminal/releases) 端末エミュレータ

- **外部コマンド**

  - linux command(sed,xargs,grep など) git for windows に入っています。

    > よくわからんけどカスタマイズは試してみたいという人は GitForWindows ポータブル版を
    > 落としてきて任意のディレクトリに置き、以下の設定をすれば PPc 上からはパスを通した状態になります。

    ```text
    KC_main = {
    FIRSTEVENT  , *set path+=【任意のディレクトリ】\git\usr\bin
    }
    ```

  - bat [github](https://github.com/sharkdp/bat)
  - colortool [github](https://github.com/microsoft/terminal/releases/tag/1904.29002)
  - ffmpeg [ffmpeg.org](https://ffmpeg.org)
  - fzf [github](https://github.com/junegunn/fzf)
  - jvgrep [github](https://github.com/mattn/jvgrep/releases)
  - nkf [vector](https://www.vector.co.jp/soft/win95/util/se295331.html)
  - peco [github](https://github.com/peco/peco/releases)
  - rclone [RCLONE](https://rclone.org/)
  - reces [frostmoon](http://frostmoon.sakura.ne.jp/)
  - ripgrep [github](https://github.com/BurntSushi/ripgrep/releases)
  - zoxide [github](https://github.com/ajeetdsouza/zoxide)

- **外部ライブラリ**
  - bregonig [k-takata](http://k-takata.o.oo7.jp/mysoft/bregonig.html) 正規表現
  - migemo [kaoriya](https://www.kaoriya.net/software/cmigemo/) ローマ字検索
  - 7-zip64.dll,7z.dll [frostmoon](http://frostmoon.sakura.ne.jp/) 統合アーカイバ

---

Powerd by [Jekyll](http://jekyllrb-ja.github.io/).
The theme forked [monos](http://jekyllthemes.org/themes/monos/).
The icons from [iconmonstr](https://iconmonstr.com/).
