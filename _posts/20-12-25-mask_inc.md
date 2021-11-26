---
layout: post
title: エントリマスクでインクリサーチ
version: PPx181以降
date: 2021-02-13
comment: PPcの下端位置に合わせてダイアログの位置を設定するようにした。
categories: PPc
---
### 説明
エントリマスクをEverythingやPecoのようなインクリメンタルサーチにする設定。
 - PPXMES\.DLL,PPXWIN\.DLLを使用。<BR>
 - [migemo](https://www.kaoriya.net/software/cmigemo/)の設定が必要。

### 使いかた
 - migemoを使えるようにしておく。
 - リアルタイムMはONにしておく。
 - 起動して適当な検索語を入力。

#### キーバインド
PPc

| KEY | COMMAND |
|:-:|:-|
| F | エントリマスク呼び出し |

PPcキーバインド
```clean
KC_main = {
F , *if %*windowrect(%N,b)<500 %: *linecust win_pos,K_edit:FIRSTEVENT,*windowposition %%*findwindowtitle("エントリマスク"),%%*windowrect(%%NC#,l),%%*windowrect(%%NC,b) %%: *js "PPx.Execute('*linecust win_pos,K_edit:FIRSTEVENT,')"
    %k"o':'ed','r':'
    %K"@F" %: *deletehistory m,0
}
```
