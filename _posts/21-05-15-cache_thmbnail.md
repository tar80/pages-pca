---
layout: post
title: サムネイルキャッシュの制御
version: PPx181+3以降
date: 2021-11-23
comment: "コマンドを更新。*zoomを使用"
categories: PPc
---
### 説明
PPcのサムネイルキャッシュを手動で制御するための設定。
- 実行したディレクトリでのみキャッシュを生成できます。
- 実行したディレクトリ内のキャッシュを全削除できます。

### 使い方
- 最初にサムネイルを適用したい表示形式に変更する。サムネイルのサイズは表示形式に影響されることに留意。
- コマンドを実行すると表示中の画像のサムネイルが生成される。一度キャッシュを削除したうえで実行するのでキャッシュ済みのサムネイルは再生成される。
- ディレクトリを移動するとLOADEVENTが実行され、自動でコマンド実行前の画像設定(XC\_ocig)に戻る。

#### :wrench:設定
```clean
;PPcメニュー
ThumbCache on   = *js "for(var i=0,l=PPx.EntryDisplayCount;i<l;i++){if(PPx.Entry(i).Size){PPx.Execute('*delete ""'+PPx.Entry(i).Name+':thumbnail.jpg""');}}"
                  *string o,ocig=%*getcust(XC_ocig)
                  *setcust XC_ocig=%*regexp("%so"ocig"","s/(\d,\d,\d),\d,(.*)/$1,1,$2/")
                  *zoom +0
                  *linecust thumbcache,KC_main:LOADEVENT,*setcust XC_ocig=%so"ocig" %%: *linecust thumbcache,KC_main:LOADEVENT,
                  %K"@LOADCUST"
ThumbCache off  = *setcust XC_ocig=%*regexp("%*getcust(XC_ocig)","s/(\d,\d,\d),\d,(.*)/$1,0,$2/")
                  *linemessage !"サムネイルキャッシュ無効
ThumbCache del  = *js "for(var i=0,l=PPx.EntryDisplayCount;i<l;i++){if(PPx.Entry(i).Size){PPx.Execute('*delete ""'+PPx.Entry(i).Name+':thumbnail.jpg""');}}"
                  *linemessage !"サムネイルキャッシュ削除
```
