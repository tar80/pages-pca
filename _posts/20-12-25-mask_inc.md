---
layout: post
title: エントリマスクでインクリサーチ
version: PPx181以降
date: 2021-12-21
comment: コマンドマクロを修正。
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
```
KC_main = {
F , *if 500>%*windowrect(%N,b) %: *linecust win_pos,K_edit:FIRSTEVENT,*windowposition %%*findwindowtitle("エントリマスク"),%%*windowrect(%%NC#,l),%%*windowrect(%%NC,b) %%: *linecust win_pos,K_edit:FIRSTEVENT,
    %k"o':'ed','r':' %: *maskentry -temp |
    *deletehistory m,0
}
```
