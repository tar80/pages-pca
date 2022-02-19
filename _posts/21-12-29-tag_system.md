---
layout: post
title: タグシステム
version: PPx182+4以降
date: 2022-01-03
comment: スクリプトとキーバインドを修正。PPvのみ補完リストに%su"taglist"を読み込むようにした。
categories: PPc
---
### 説明
リストファイルのコメント欄にタグを書き込んで運用するシステム。<BR>
本格的なタグ管理はできませんが手軽に使えます。次のような機能を持ちます。
- 複数のエントリに対するタグの追加・削除。
- 複数のタグリストの切り替え。
- [commentSearch.js]({{ site.baseurl }}{% post_url 21-01-09-script_commentSearch %})を使って&-or検索で絞り込める。
- リストファイル外でも使える。
- タグに色付けしてPPvで表示。(おまけ機能)

> - スクリプト内で[compCode.js]({{ site.baseurl }}{% post_url 20-12-22-script_compcode %})を使用。
> - スクリプト内おまけ機能でWindowModuleを使用。

**タグ用メニューの説明**

| 項目 | 内容 |
|:-|:-|
| 1:TagSys | 一行編集が起動してタグリストを選択できます。<BR>通常、一行編集には`%'list'%\tag\`が挿入されますが、カレントディレクトリに`01_TAG.TXT`があればそちらを挿入します。選択したパスが存在しないときは`_default.txt`を読み込みます。<BR>リスト選択後にメニュー下部にタグ項目が追加されます。 |
| A:add | 一行編集で選択したタグをエントリに追加します。<BR>スペース区切りで複数のタグを追加できます。 |
| R:remove | 一行編集で選択したタグをエントリから削除します。<BR>スペース区切りで複数のタグを削除できます。 |
| E:edit&nbsp;taglist | 現在選択中のタグリストを編集します。保存と同時にメニューを再生成します。 |
| V:tag view | (おまけ機能)<BR>カーソル移動するとPPv[T]に色付けされたタグが表示されます。再実行で解除。<BR>※カーソル移動ごとにファイルを読み書きしているので連続移動するとエラーがでます。<BR>  `*entrytip`に組み込むのがいいかもしれません。 |

> - 1:TagSysからタグリストを選択すると\_User:taglistにパスが設定されます。
> - タグ項目の生成では一つのタグにつき、二つの項目が追加されます。`-`が付いた項目は削除用です。
> - 重複するタグは一つにまとめられます。

### 使い方
1. tagMakeMenu\.jsの初期設定をする。
1. `*edit -new -lf -utf8bom %'list%\tag\_default.txt`(初期値。スクリプト内変数TAG_LISTと同一のパス)を実行してタグリストを生成する。(必須)
1. 2.のファイルに使いたいタグを書き込む。タグは一行で1タグと認識される。
1. 次にタグ用のメニューを呼び出し、`1:TagSys`を押すと一行編集が起動するので`_default.txt`を選択するとメニューにタグが追加される。<BR>
1. 適当にエントリにタグを付けていく。
1. コメントサーチで絞り込む。

[タグリスト作成とメニュー生成までの手順.gif]({{ site.baseurl }}{% link /public/img/tag_system1.gif %})<BR>
[タグ付けとタグの絞り込み.gif]({{ site.baseurl }}{% link /public/img/tag_system2.gif %})
<BR><BR>
**タグの書式**

`タグ ;アルファベット一文字 ;数字`<BR>
最初のコメントはメニューのショートカットキー、二つ目のコメントはansi色番号を指定できる。
- `あとで読む ;a` これはメニューに`A: あとで読む`として登録される。
- `いますぐ読め! ;いま！` これはメニューには登録されない。
- `緑 ; ;32`  `V:tag view`でPPvに表示されるタグが[<span style="color:green;">緑</span>]のように色づけされる。

> ansi色番号は`ansi color`などでWeb検索して調べて下さい。



#### キーバインド

| KEY | COMMAND |
|:-|:-|
| [ | コメント |
| CTRL+[ | コメントサーチ |
| CTRL+S(一行編集) | 編集中のタグを保存。 |

#### 設定
```
A_exec = {
scr  =    ;スクリプトをまとめておくディレクトリパス
cfg  =    ;PPxの設定ファイルをまとめておくディレクトリパス
list =    ;読み書きするリストをまとめておくディレクトリパス
}

; キーに割り当てる
-|M_Ueco =
M_Ueco = {
&1:TagSys = *string o,basepath=%'list'%\tag\
            *ifmatch "o:e,a:d-",01_TAG.TXT %: *string o,basepath=01_TAG.TXT
            *string o,listpath=%*input("%so'basepath'" -title:"Select Taglist.." -k *completelist)
            *script %'scr'%\tagMakeMenu.js,%so"listpath"
-- =
ex = ??M_UecoSub
}

KC_main = {
'['   , *string o,cmnt=%*comment
        *ifmatch *"*,0%so"cmnt" %: *string o,cmnt=%*regexp(%so"cmnt","/""/""""""""/g")
        *comment extract,"%*input("%so'cmnt'" -title:"Comment.." -mode:Re -k *mapkey use,K_tagSysMap)"
^'['  , *script %'scr'%\commentSearch.js,filter
}

KV_img = {
'[' , *string o,cmnt=%*extract(C"%%*comment")
      *ifmatch *"*,0%so"cmnt" %: *string o,cmnt=%*regexp(%so"cmnt","/""/""""""""/g")
      *ifmatch !0,0%su"taglist" %: *string o,comp=*string e,filename=%su"taglist" %%: *completelist -set -file:%su"taglist" -detail:"user1" %%:
      *string o,cmnt=%*input("%so"cmnt"" -title:"Comment.." -mode:Re -k %so"comp" *mapkey use,K_tagSysMap)
      *execute C,*comment extract,"%so"cmnt"
}

-|K_tagSysMap =
K_tagSysMap = {
^S  , *script %'scr'%\appendText-utf8lf.js
      *linemessage save tag
      *script %'scr'%\tagMakeMenu.js
}
```
<BR><BR>
**スクリプトの説明**

- appendText-utf8lf.js 一行編集からタグリストにタグを保存するときに使う。
- tagColor.js `_User:taglist`を元に色付きタグファイル`%'temp'%\ppxtags.tmp`を生成する。
- tagMakeMenu.js `_User:taglist`を設定。それを元にタグ用メニュー`zzUecoTagSys.cfg`を生成し読み込む。
- tagOperate.js エントリにタグを追加・削除する。

<script src="https://gist.github.com/tar80/6b3eb9b49541b1cee39b529b2ab0e2f0.js"></script>
