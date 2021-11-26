---
layout: post
title: テンプレートを使ったリネーム一括処理
version: PPx181以降
date: 2021-11-23
comment: selStr.js -> setSel.jsに変更。
categories: PPc
---
### 説明
SHIFT+Rキー(リネーム一括処理)上で呼び出すリネームテンプレート。

> - 文字列の選択には[setSel.js]({{ site.baseurl }}{% post_url 2020-12-26-script_setSel %})を使用。
> -  コマンド内で%\*padding(PPXTEXT.DLL)を使用。
> - 数字桁揃えはファイル名の【○○○数字.拡張子】の形式で、【数字.拡張子】部分が引き継がれます。<BR>
他の形式だと誤爆します。実行する前に試行(T)で確認してください。

### 使い方
 - PPc上で`%K"@\R"`(詳細リネーム)を呼び出す。
 - ショートカットキー、またはAPPSキーなどに登録したテンプレートを呼び出す。
 - 先に文字を入力しておけば、なんかいい感じで選択状態になる。

#### 設定
```clean
;エイリアス
_exec = {
scr  = ;スクリプト親ディレクトリパス
}

;ポップアップメニュー
M_editcRename = {
前に追加(&F)   = *replace :%*edittext()%%C
                 *script %'scr'%\setSel.js,"(\:)(.*)%%C"
後ろに追加(&B) = *replace :%%X%*edittext().%%T
                 *script %'scr'%\setSel.js,"(\:%%X)(.*)\.%%T"
置換(&R)       = *linemessage s/対象文字/置換文字/[g][I]   ※:%%展開をOFF
                 *replace s/%*edittext()//
                 *script %'scr'%\setSel.js,"(s\/)(.*)\/\/"
連番(&N)       = *linemessage [\]=連番マクロ
                 *replace :%*edittext()\.%%T
                 *script %'scr'%\setSel.js,"(\:)(.*)\\\.%%T"
数字桁揃え(&S) = *string o,digit=%*input("3" -title:"桁数" -mode:e)
                 *replace ":%*edittext()%%*padding(%%*regexp(""""%%FXN"""",""""s/\D*(\d*)/\1/""""),%so"digit",0).%%T"
                 *script %'scr'%\setSel.js,"(\:)(.*)%%\*padding.*"
拡張子一括(&E) = *replace :%%X.%T
                 *script %'scr'%\setSel.js,"(.*%%X\.)(.*)"
}
```
