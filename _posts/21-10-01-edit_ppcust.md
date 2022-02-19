---
layout: post
title: PPcustで設定編集
version: PPx181以降
date: 2021-11-23
comment: ^\@のキーバインドを修正。popup.jsは不要になった。
categories: PPcust
---

### 説明

[PPeで簡単なコード編集]({{ site.baseurl }}{% post_url 20-12-27-edit_ppe %})ではPPeを使ったコード編集に触れましたが、
`*ppcust -edit`でも同等のコード編集ができます。
こちらはPPe版と比較して、

- 一部のコマンドをK\_ppeからK\_editに移動したので全体で動作するようになった。
- PPx設定パラメータを簡易ポップアップするキーバインドを追加。
- 現在使用中の全設定登録名(KC\_mainなど)を補完リストに読み込むキーバインドを追加。
  - 補完リストは一度PPcustで書き出した設定ファイルをgrepもしくはripgrepを使って整形。
  - PPcustで出力するファイルはUTF-8BOMにするなどコマンドが読める文字コードにしておく必要あり。
  - grepのほうはコマンド内でtab文字を使っているのでスペースに変換しないように注意。

> コマンド内で[compCode.js]({{ site.baseurl }}{% post_url 20-12-22-script_compcode %})を使用。

### 使い方

- 設定編集を起動。`%Obd *cd %0 %: *ppcust -edit`
- F12をポチっと。
- 目当ての設定を補完して編集。

![sample]({{ site.baseurl }}{% link /public/img/edit_ppcust.gif %})

#### キーバインド

##### PPcust

| KEY | COMMAND |
|:-|:-|
| F12 | 補完リストを生成して読み込み |
| CTRL+: | 一行編集簡易補完 |
| CTRL+; | 行選択 |
| CTRL+SHIFT+@ | 選択範囲設定名の内容をポップアップ |
| CTRL+SHIFT+: | 選択範囲コマンド実行 |
| CTRL+N | 補完リストを下方向に移動 |
| CTRL+P | 〃        を上方向に移動 |


> ctrl+shif+@は`KC_main`などの編集中テキストを選択した状態で実行すると
文字数制限のある簡易ポップアップを表示。

#### 設定

```text
;エイリアス
A_exec = {
cfg     = ;PPxの設定ファイルをまとめておくディレクトリパス
list    = ;読み書きするリストをまとめておくディレクトリパス
}

;PPcustの出力文字コード
X_ccode = 2

;編集全体
;【】内を変更する
; grepコマンド内のtab文字は変換しないように注意
; ripgrepを使う場合は、grepの行と置き換える
K_edit  = { ; 一行編集/PPe共用,K_lied,K_ppeが優先
F12     , *stop !%*findwindowtitle(編集した内容の取込)
          *string o,backup=【%0backup\PPX.CFG】
          *string o,ckeys=【%'list'%\ppx_cfgkeys.txt】
          %Osdb *cd %0 %: *ppcust CD %so"backup"
          grep -E "^(([ABCEFHKMPSVX][BCEVTt]?_)|Mes|_)[^ ]*     = " "%so"backup"" > %so"ckeys"
          *completelist -file:"%so"ckeys""
;          rg -N --color never -s -e "^(([ABCEFHKMPSVX][BCEVTt]?_)|Mes|_)[^\s]*\t=\s" "%so"backup"" > %so"ckeys"
;ctrl + ":"
^V_HBA  , *insert "%*script(%'scr'%\compCode.js,"ih","""","Complete..")"
;shift + ctrl + @
^\V_HC0 , *js "PPx.Echo(PPx.Extract('%%*getcust(%%*selecttext)'));"
;shift + ctrl + :
^\V_HBA , *execute C,%*selecttext()
;ctrl + ";"
^V_HBB  , %k"HOME \END"
^N      , *if 0%*editprop(list) %: %K"@DOWN" %: *stop
          *completelist
^P      , *if 0%*editprop(list) %: %K"@UP" %: *stop
          *completelist
}
```

<!-- popup\.js                                         -->
<!-- ```javascript                                     -->
<!-- PPx.Echo(PPx.Extract('%*getcust(%*selecttext)')); -->
<!-- ```                                               -->
