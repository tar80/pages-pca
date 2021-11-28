---
layout: post
title: PPeで簡易コード編集
version: PPx181以降
date: 2021-03-30
comment: キーバインドを追加した。
categories: PPe
---
### 説明
数行程度のちょっとしたコマンドマクロを作るときにあると便利なPPe設定集。
- 簡易補完機能あり。
- 選択範囲のコマンド実行機能完備。
- 即コマンド登録可能。バックアップは%'cfg'に@add\_command.cfgとして書き出し。

> - コマンド内には[compCode.js]({{ site.baseurl }}{% post_url 20-12-22-script_compcode %})、[result.js]({{ site.baseurl }}{% post_url 20-12-18-script_result %})を使用。
> - 補完機能とコマンド登録は誤爆する危険性があります。ご利用の際はお気をつけ下さい。
> - コマンド追加用のマクロはPPeのメニューバー以外に登録してください。メニューバーから実行すると失敗します。

### 使い方
PPeでコマンドを打って遊ぶ。
<BR><BR>
![sample]({{ site.baseurl }}{% link /public/img/edit_ppe.gif %})

#### キーバインド
**PPe**

| KEY | COMMAND |
|:-:|:-:|
| CTRL+: | 一行編集簡易補完 |
| CTRL+; | 行選択 |
| CTRL+SHIFT+: | 選択範囲コマンド実行 |
| CTRL+N | 補完リストを下方向に移動 |
| CTRL+P | 〃        を上方向に移動 |

#### 設定
```clean
;エイリアス
A_exec = {
cfg  = ;設定ファイル親ディレクトリパス
}

;一行編集
K_edit  = { ; 一行編集/PPe共用,K_lied,K_ppeが優先
^V_HBA , *insert "%*script(%'scr'%\compCode.js,"i","""","Complete..")"  ;ctrl + ":"
^V_HBB , %k"HOME \END" ;ctrl + ";"
}

;PPe
K_ppe = {
\^V_HBA , *execute C,%*selecttext() ; shift + ctrl + ":"
^N      , *if 0%*editprop(list) %: %K"@DOWN" %: *stop
          %K"@F4"
^P      , *if 0%*editprop(list) %: %K"@UP" %: *stop
          %K"@F4"
}

;キー又はポップアップメニュー
コマンド追加(&A)      = *setcust M_editCadd:%*input(-title:"コマンド名の登録")=%*RESULT(shapecode)
                        %On *ppcust CD %'cfg'%\@add_command.cfg -mask:M_editCadd
コマンド削除(&D)      = *deletecust "M_editCadd"
-- =
addCMD                = ??M_editCadd
```

