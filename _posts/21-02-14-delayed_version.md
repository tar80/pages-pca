---
layout: post
title: バージョンアップ後の半自動化処理
version: PPx181以降
date: 2021-09-01
comment: 更新情報の書き出しをやめた。
categories: PPc
---
### 説明
バージョンアップ時に情報行のバージョン表記を更新し、ついでにhelp.htmlをダウンロード。
- バージョンアップ実行時に遅延実行を登録し、情報行のバージョン表記を書き換えます。
- 短所として、アップデートキャンセル時にも遅延実行が登録されてしまいますが、遅延実行自体はキャンセルできます。

### 使い方
- PPxをアップデートすると遅延実行が登録される。
- 次回PPc起動時に確認ダイアログが出るのでOKする。
  > 行情報の初回登録時に、`*execute %*getcust(S_infobar:cnts)`する必要があります。
  設定ファイル内にACTIVEEVENTか\_Delayedを書いておけば自動実行されます。

### 設定
※【】内を環境に合わせて書き換える。
```clean
;ここに適用したい行情報を記述。
S_infobar = {
cnts =*customize 【XC_stat】=【_AUTO,_AUTO,0,0,I"Version:" 】i"%*js(PPx.Result=PPx.PPxVersion;)" 【s0】
}

;初回登録の例
KC_main = {
ACTIVEEVENT , *execute %*getcust(S_infobar:cnts) %: *deletecust KC_main:ACTIVEEVENT
}
_Delayed = {
execute = *execute %*getcust(S_infobar:cnts)
}

;メニュー
アップデート(&U) = *checkupdate p
                   %Oi *setcust _Delayed:execute=*execute %*getcust(S_infobar:cnts) %%: *httpget "http://toro.d.dooo.jp/ppxhelp.html","【%0%\】ppxhelp.html" %%: *httpget "http://toro.d.dooo.jp/ppxindex.html","【%0%\】ppxindex.html" %%& *httpget "http://toro.d.dooo.jp/ppxwords.html","【%0%\】ppxwords.html"
```
