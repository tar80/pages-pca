---
layout: post
title: Everythingと連携
version: PPx182以降
date: 2021-11-14
comment: PPx181+3の更新に対応。
categories: PPc
---
### 説明
[Everything](https://www.voidtools.com/)とPPxの連携について下記二通りの方法を説明しています。
- Everythingの検索結果をPPcに渡す。
- [Everything Search Module](http://toro.d.dooo.jp/slppx.html#ppxets)を使ってデータベースを操作する。

**検索結果をPPcに渡す**<BR>
Everythingから外部にパスを渡すコマンド枠が
5つ用意されているので、これに設定を割り当てれば、ENTERでフォーカスのあるPPcへ、SHIFT+ENTERで反対窓へ対象パスを送れます。

```ini
;Everything.ini
;@PPcのパスは書き換える
open_folder_command2=$exec(@\PPCW.EXE -r -k *jumppath "$pathpart(%1)" -entry:"$namepart(%1)")
open_file_command2=$exec(@\PPCW.EXE -r -k *jumppath "$pathpart(%1)" -entry:"$namepart(%1)")
open_path_command2=
explore_command2=$exec(@\PPCW.EXE -r -k *execute ~,*jumppath "$pathpart(%1)" -entry:"$namepart(%1)")
explore_path_command2=$exec(@\ppx\PPCW.EXE -r -k *execute ~,*jumppath "$pathpart(%1)" -entry:"$namepart(%1)")
file_open_keys=8205
file_explore_path_keys=9229
file_open_path_keys=
```
<BR>
**Everything Search Moduleを使う**<BR>
まずモジュールの説明。
- ETSとETPの二種類用意されています。ここではETPを使用。
  > Everythingの対応バージョンは1.3となっていますが、1.4のeverythingでも使えています。
- 利用にはEverythingの起動とETPサーバの登録が必要。

次いで機能の説明。Everythingのデータベースを、
- 一行編集の補完候補として利用する。
- \*whereコマンドの検索対象として利用する。

上述の通り、Everything Search Module(PPXETP\.DLL)を使うにはEverythingをETPサーバとして起動しておく必要があり、
またデータベースの作成に管理者権限が求められます。普段ポータブル設定で使っていてサービス機能を登録していない場合は、[管理者権限PPb]({{ site.baseurl }}{% post_url 21-09-25-highest_ppb %})の記事を参考にすれば
UAC認証のダイアログを回避できます。<BR>
ETPサーバは通常とは別インスタンス(別の設定とデータベース)なので、PPc専用として検索対象を絞り込んでおくと使い易くなります。
なお、Everything lite版はETPサーバ機能が除外されているため、通常版を利用します。

### 使い方
- `%K"@^F"`で一行編集と連携。everythingのデータベースを使用してのパス検索。<BR>
  通常の一行編集では候補を表示しきれないので全画面で表示。<BR>
  こんな感じ。<BR>

![sample]({{ site.baseurl }}{% link /public/img/everything1.png %})
![sample]({{ site.baseurl }}{% link /public/img/everything2.png %})

- `%K"@^W"`で\*whereコマンドと連携。普通に\*whereコマンドからモジュール検索を実行するとちょっと不便なので、%\*input()の結果を
  \*whereisする。別途用意したETOPTION.TXTを読み込めばEverythingのプレフィクス(file:など)を補完できる。

### 設定

**PPx**
```clean
A_exec = {
list    = ;読み書きするリストをまとめておくディレクトリパス
}

_User = {
ETP_PORT = 【ポート番号】
ETP_USER =        ; ユーザ名
ETP_PASS =        ; パスワード
ETP_FULL = 10000  ; 詳細検索時の最大回答数(PPc の Where is)
ETP_PART = 1000   ; 簡易検索時の最大回答数(一行編集のパス補完)
}

;【】を編集。
KC_main = {
;ETPサーバの起動設定
FIRSTEVENT , *if !%*findwindowclass(EVERYTHING_%(ETP%)) %: 【管理者権PPb.vbs】-c "*run -d:【Everythingのインストールディレクトリ】 Everything.exe -minimized -admin -instance ETP -connect localhost:【ポート番号】 -drive-links"

;一行編集everything Search
^F , *string o,xflst=%*getcust(X_flst)
     *linecust win_pos,K_edit:FIRSTEVENT,*setcust X_flst=%so"xflst" %%: *string o,id=%%*findwindowtitle("everything-ETP") %%: *windowposition %%so"id",【0】,【0】 %%: *windowsize %%so"id",【窓サイズ横幅】,【縦幅】 %%: *linecust win_pos,K_edit:FIRSTEVENT,
     *setcust X_flst=%*regexp("%so"xflst"","s/(\d,\d,\d*),\d*/$1,【候補リスト行数】/")
     *string o,path=%*input("" -title:"everything-ETP" -mode:e -k *completelist -set -detail:"user module" -file:"%%'list'%\ETOPTION.txt")
     *jumppath %*name(D,%so"path") -entry:%*name(C,%so"path")

;*whereコマンド
^W , *whereis -text:"%*input("%FD " -title:"ETP Search" -mode:s -k *completelist -set -file:%'list'%\ETOPTION.TXT)" -type:5

;option付き
^W , *linecust DelEtSi,KC_main:ACTIVEEVENT, *string i,Edit_OptionCmd= %%: *string i,EtPf= %%: *linecust DelEtSi,KC_main:ACTIVEEVENT,
     *string i,Edit_OptionCmd=*string i,EtPf=%%*input("%%si"EtPf"" -title:"Prefix" -mode:e -k *completelist -file:"%%'list'%\ETOPTION.TXT") %%: *setcaption ETP Search   Prefix:%%si"EtPf"
     *execute ,*whereis -text:"%%si"EtPf"%*input("%FD*" -title:"ETP Search" -mode:Os)" -type:5 -listfile:"%'temp'%\ppxresult.xlf"

;Everythingの起動設定。
key , *if 0%*findwindowclass(EVERYTHING) %: *focus #%*findwindowclass(EVERYTHING) %: *stop
      *run -d:【Everythingのインストールディレクトリ】 Everything.exe
}
```

<BR>
**Everything**

```ini
; 必要そうな部分のみピックアップ
; ※上書き利用はできません

;Everything-ETP.ini
run_as_admin=1
window_title_format=$i Server
match_whole_word=0
match_path=0
match_case=0
match_diacritics=0
match_regex=0
etp_server_enabled=1
etp_server_bindings=127.0.0.1
exclude_folders="*\\.git","*\\.github","*\\node_modules","*\\deleted$"
```
