---
layout: post
title: エントリマスクでインクリサーチ
version: PPx181以降
date: 2023-12-03
comment: コマンドマクロを修正。
categories: PPc
---
### 説明

エントリマスクをEverythingやPecoのようなインクリメンタルサーチにする設定。

- PPXMES\.DLL,PPXWIN\.DLLを使用。
- [Migemo](https://www.kaoriya.net/software/cmigemo/)の設定が必要。

### 使い方

- Migemoを使えるようにしておく。
- 起動して適当な検索語を入力。

#### キーバインド
PPc

| KEY | COMMAND |
|:-:|:-|
| F | エントリマスク呼び出し |

PPcキーバインド

```clean
KC_main = {
F , *string o,rmsk=%*getcust(XC_rmsk)
    *if 600>%*windowrect(%N,b)%:*linecust inc_pos,K_edit:FIRSTEVENT,*windowposition %%*findwindowtitle("%*getcust(Mes0411:TFEM)"),%%*windowrect(%%NC,l),%%*windowrect(%%NC,b)
    *linecust inc_mode,K_edit:FIRSTEVENT,*insert o:edw,r:%%:*editmode e%%:*setcust XC_rmsk=%so'rmsk'%%:*linecust inc_pos,K_edit:FIRSTEVENT,%%:*linecust inc_mode,K_edit:FIRSTEVENT,
    *setcust XC_rmsk=1,%*rightstr("%so'rmsk'",4)
    *maskentry -temp |
    *deletehistory m,0
}
```

一行目 リアルタイム検索の設定値を取得。
二行目 エントリマスクのFIRSTEVENTに位置調整を設定。現在窓の下辺が指定値を越えるときは設定しません。
三行目 エントリマスクのFIRSTEVENTに初期入力文字と参照モード、終了処理を追加設定。
四行目 リアルタイム検索をONに設定。
五行目 エントリマスクを実行。
六行目 入力履歴を削除。
