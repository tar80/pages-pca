---
layout: post
title: "AUX:Rclone"
version: PPx181以降
date: 2022-01-03
comment: "ログ取得中の通知メッセージを*jobコマンドに変更した。"
categories: PPc @AUX
---
### 説明
aux:パス機能を用いてRcloneと連携するための設定。<BR>
公式[ppxaux06](http://toro.d.dooo.jp/slppx.html#ppxaux)をそのまま導入しても使えますが、ここではより便利に使うための設定を模索しています。

<BR>
<details><summary>Rcloneについて</summary><div>
  <ul>
    <li>Rcloneはオンラインストレージを操作するためのコマンドラインツールです。<BR>
    有名どころのサービスは概ね対応していて、onedriveもgdriveもdropboxもamazon_S3も
    専用のアプリケーションは必要とせず、マウントする必要も無く、同様の操作で扱えます。
    </li>
    <li>通常%userprofile%\.config\rcloneに作られる設定ファイルはトークンなどがそのまま書き出されますが、
    パスワードを設定して暗号化することもできます。
    </li>
  </ul>
</div></details>
<BR>

このカスタマイズは、aux: sample R6(ppxaux06)と比較すると以下のような特徴があります。
- 設定ファイルの暗号化に対応。PPc起動後の最初の読み込み時にパスワードを要求される。
- オンラインストレージのルートパスより上層へ移動しようとしたときに
  Rcloneでローカルパスを開くのを抑制。
- リストの生成は、通常生成とスクリプト経由生成の2パターン用意。<BR>
  > スクリプト版はaux:パスの仕様上、実行時に一度リストを読み込み、更新後に再度読み込む処理をしています。
  また、仕様を逸した利用方法なので誤爆やエラーの可能性が大いにあります。
  PPxの更新によっては使えなくなることも考えられます。

  | 実行状態 | 通常版 | スクリプト版 |
  |:-|:-|:-|
  | list実行時のPPc | ロックされる | ロックされない |
  | overup実行時 | リスト再読み込み | メッセージを表示 |
  | PPb | 非表示 | 最小化 |
  | パスワードを間違えた時 | PPbやls2lfのプロセスが残る<BR>※PPb非表示をやめれば回避できる | PPcの再起動が必要 |

> カスタマイズにはPPxKeyModuleとls2lf\.exe(ppxaux06)が必要です。


### 使い方
`*jumppath aux://S_auxRCLONE/(Rcloneの設定名):`または`%K"@^L"`などでパスに移動。<BR>
> aux:パスを開く時に%si"RootPath",%si"stragePath",Rclone用キーバインドが設定され、
閉じるときに削除されます。

#### キーバインド
PPc

| KEY | COMMAND |
|:-:|:-|
| @F5 | リスト更新 |
| @BS | 上層へ移動 |
| @C | コピー |
| @M | 移動 |
| @D | 削除 |
| @CTRL+K | ディレクトリ作成 |
| V | エントリをPPv標準入力で開く |

#### 設定

```
A_exec = {
scr  =  ;スクリプトをまとめておくディレクトリパス
list =  ;読み書きするリストをまとめておくディレクトリパス
}

; 標準出力を使った直接表示。小さいファイルサイズのみ読み込める
; ※ppxやrcloneのバージョン、使用するsusiePluginによっては画像の読み込みができないかもしれません
K_rcloneMap = {
V , %Oq *execute ba,*job start %%: rclone cat %*regexp("%si"stragePath"%*addchar(/)","/:\//:/")%R | %0ppvw %%& *job end
}
```
<BR>
通常版
```
S_auxRCLONE = {
base    = aux://S_auxRCLONE/ %; remote:/path
cmd     = rclone.exe
lf      = %'list'%\rclone.xlf
ls2lf   = %0%\auxcmd\ls2lf.exe
setAux  = *execute %n,*string i,RootPath=aux://S_auxRCLONE/%*regexp("%*path","/^(.*:).*/$1/") %%: *string i,stragePath=%*path %%: *mapkey use,K_rcloneMap %:
; パスワード不要なら一行目*ifmatch 0,0'RCLONE~の行全体を削除
list    = *ifmatch 0,0%'RCLONE_CONFIG_PASS' %: %k"@TAB" %: *set RCLONE_CONFIG_PASS=%*pass
          %*setAux
          %Obd %*ls2lf -j "A:Attr,S:Size,W:ModTime,F:Name" %*lf rclone lsjson "%*path%*addchar(/)"
          %*lf
overup  = *string i,NewPath=%si"RootPath" %:
leave   = *string i,stragePath= %: *mapkey delete,K_rcloneMap %:
get     = *execute C,*logwindow "copy %*src" %: %*cmd copy "%*path%*addchar(/)%*src" "%*name(D,%*dest)" %:
get-m   = *execute C,*logwindow "move %*src" %: %*cmd move "%*path%*addchar(/)%*src" "%*name(D,%*dest)" %:
store   = *execute C,*logwindow "copy send %*path" %: %*cmd copy "%*src" "%*path%*addchar(/)" %:
store-m = *execute C,*logwindow "move send %*path" %: %*cmd move "%*src" "%*path%*addchar(/)" %:
copy    = *execute C,*logwindow "copy %*src send %*dest" %: %*cmd copy "%*path%*addchar(/)%*src" "%*dest" %:
move    = *execute C,*logwindow "move %*src send %*dest" %: %*cmd move "%*path%*addchar(/)%*src" "%*dest" %:
makedir = *execute C,*logwindow "makedir %*path" %: %*cmd mkdir "%*path" %:
del     = *execute C,*logwindow "delete %*path" %: %*cmd delete "%*path" %:
deldir  = *execute C,*logwindow "remdir %*path" %: %*cmd rmdir "%*path" %:
}
```
<BR>
スクリプト版
```
S_auxRCLONE = {
base    = aux://S_auxRCLONE/ %; remote:/path
cmd     = rclone.exe
lf      = %'list'%\rclone.xlf
ls2lf   = %0%\auxcmd\ls2lf.exe
code    = %'scr'%\aux_main.js
setAux  = *execute %n,*string i,RootPath=aux://S_auxRCLONE/%*regexp("%*path","/^(.*:).*/$1/") %%: *string i,stragePath=%*path %%: *mapkey use,K_rcloneMap %:
; パスワード不要なら三行目*ifmatch 0,0'RCLONE~の行全体を削除
list    = *if 0%sp"runJsAuxMain" %: *stop 0==0%si"within" ;スクリプト二重実行の防止
          *if %*keystate(226)%*keystate(220)%*keystate(116)%*keystate(13)%*keystate(9)%*keystate(8) %: *string i,auxID=%n ;特定キー押下時リスト更新フラグ keystate=226:\_, 220:\|, 116:F5, 13:ENTER, 9:TAB, 8:BS
          *ifmatch 0,0%'RCLONE_CONFIG_PASS' %: %k"@TAB" %: *set RCLONE_CONFIG_PASS=%*pass
          *ifmatch !0,0%si"auxID" %: *script %*code,rclone,%*lf,%*path,0,%si"auxID"
          %*setAux
          *string i,auxID= %: *string i,within= %: %*lf
overup  = *string i,NewPath=%si"RootPath" %: *linemessage !"<<root>> %: *wait 1000,2 %:
leave   = *string i,stragePath= %: *mapkey delete,K_rcloneMap %:
get     = *string i,auxID=%n %: *execute C,*logwindow "copy %*src" %: %*cmd copy "%*path%*addchar(/)%*src" "%*name(D,%*dest)" %:
get-m   = *string i,auxID=%n %: *execute C,*logwindow "move %*src" %: %*cmd move "%*path%*addchar(/)%*src" "%*name(D,%*dest)" %:
store   = *string i,auxID=%~n %: *execute C,*logwindow "copy %*src send %*path" %: %*cmd copy "%*src" "%*path%*addchar(/)" %:
store-m = *string i,auxID=%~n %: *execute C,*logwindow "move %*src send %*path" %: %*cmd move "%*src" "%*path%*addchar(/)" %:
copy    = *string i,auxID=%n %: *string i,within=1 %: *execute C,*logwindow "copy %*src send %*dest" %: %*cmd copy "%*path%*addchar(/)%*src" "%*dest" %:
move    = *string i,auxID=%n %: *string i,within=1 %: *execute C,*logwindow "move %*src send %*dest" %: %*cmd move "%*path%*addchar(/)%*src" "%*dest" %:
makedir = *string i,auxID=%n %: *execute C,*logwindow "makedir %*path" %: %*cmd mkdir "%*path" %:
del     = *string i,auxID=%n %: *execute C,*logwindow "delete %*path" %: %*cmd delete "%*path" %:
deldir  = *string i,auxID=%n %: *execute C,*logwindow "remdir %*path" %: %*cmd rmdir "%*path" %:
}
```

<BR>
<script src="https://gist.github.com/tar80/9b66e4ec4e17a4551fb35a9d7b08c172.js"></script>
