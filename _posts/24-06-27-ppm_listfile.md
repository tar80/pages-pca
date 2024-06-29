---
layout: post
title: リストファイル上の操作をサポート
version: PPx197以降
date: 2024-06-29
comment: 投稿。
repository: tar80/ppm-listfile
categories: PPc @plugin
---

### 説明

ppm-listfileを更新しました。以前書いた[リストファイルを使ったメモ]({{ site.baseurl }}{% post_url/21-09-17-command_memo %})などの機能を追加しています。  
補完リストの追加と設定ファイルの更新があるので、`*ppmInstall`と`*ppmCompare`を実行してください。

- **リストファイル書き換え機能の強化**  
  PPxのリストファイル書き換えに加え、マークとハイライト状態を保存します。
- **メモ機能**
  - リストファイル上ではカーソル位置にメモを追加します。
  - リストファイル外では指定したファイルにメモを追加します。
- **仮想エントリのリネーム機能**  
  通常、仮想エントリ(実体の存在しないエントリ)はリネームできませんが、  
  リストファイル上の仮想エントリをリネームできるようにしています。
- **仮想エントリの属性値を変更する機能**
- **リンク切れの抽出機能**

### 使い方

![sample]({{ site.baseurl }}{% link /public/img/listfile_memo.webp %})

#### 設定

リネームの設定はppm-fileoperationなど、他のプラグインでも設定される可能性があるため、  
`*linecust`を使って設定する方法を記述してあります。  
わかりづらかったら、プラグイン側の記述はコメントアウトして自分の設定ファイルに  
まとめてしまってよいですが、ここではppm-listfileとppm-fileoperationのリネーム設定を  
プラグイン側で管理する設定例を説明します。

- 始めにユーザー設定ファイルに次のように記述します
  ```text
    KC_main = {
    R , *checkoffmark
        %mlf1 *string o,dirtype=%*js("PPx.Result=PPx.DirectoryType;")
        %mlf2 *if 4==%so'dirtype'%:%K"@R"%:*stop
        *ifmatch "63;96",%so'dirtype'%:*cd %*temp%:%Obd reces /mn%C:%*input("%*regexp("%FC","s/(.*\\)(.*)/$1|$2|/")" -title:"" -mode:Ec -select:i) %FD%&%K"@^F5"%:*stop
        *ifmatch /^aux:.*/,%FDV%:%K"@R"%:*stop
        %mfo %K"@R"
    }
  ```
  1. `*checkoffmark`は、画面外にマークがあるときに警告をだします
  1. 現在開いているディレクトリの種類を取得し、`%so'dirtype'`に代入します
  1. リストファイルを開いているときは標準のリネームを実行します
  1. 書庫内のエントリはrecesを使ってリネームします
  1. aux:パスを開いているときは標準のリネームを実行します
  1. 標準のリネームを実行します
- ppm-listfileの`[linecust]` `[endlinecust]`間に次のように記述します
  ```text
  [linecust]
  lf1,KC_main:R,*string o,dirtype=%(%*script("%sgu'ppmrepo'\ppm-listfile\dist\renameLfEntry.js")%)
  lf2,KC_main:R,*if 4==%%so'dirtype'%:*stop
  [endlinecust]
  ```
  - `lf1`,`lf2`はそれぞれ、`%mlf1`,`%mlf2`と置き換えられます
  - renameLfEntry.jsは、戻り値に`PPx.DirectoryType`を返します  
    開かれているのがリストファイルのときはリネームを実行します  
    エントリが存在しない(削除済みエントリなど)ときは処理をストップします
- ppm-fileoperationの`[linecust]` `[endlinecust]`間に次のように記述します
  ```text
  [linecust]
  fo,KC_main:R,*ppcfile !rename -min -same:skip -error:dialog -undolog:on -log:off -name:"%%*input("%%C" -title:"Rename" -mode:Ec -k *cursor -8%%%%:*mapkey use,K_ppmRename)"
  [endlinecust]
  ```
  - `fo`は、`%mfo`と置き換えられます。標準のリネームとの違いはundo可能か、否かです

このように記述しておけば、プラグインの設定更新時にKC_main:Rが上書きされることなく  
設定を共存できます。
