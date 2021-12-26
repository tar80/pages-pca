---
layout: post
title: PPxでGrep
version: PPx181以降 KeyModuleR8以降
date: 2021-12-22
comment: "書き出すリストファイルパスを%*temp(result.xgrep,name)に変更。"
categories: PPc
---
### 説明
各種grepの出力結果をPPxエントリとして扱えるようにする設定集。
- 使用するコマンドを選択できます。例えばripgrepの結果はリストファイルに出力、
jvgrepの結果はPPvで見るというようなことができます。
- `"` `%`のエスケープを意識せず検索できます。
- PPvで表示時には一時的にキャレットモードに変更します。
- 検索結果にはLFexec\.js経由で指定したコマンドを実行可能。
- 検索結果は%'temp'に蓄積されるので、[同階層の隣合うディレクトリに移動]({{ site.baseurl}}{% post_url 20-12-25-move_directory %})を使うと簡単に履歴を辿れます。窓別表示形式をGREPに変更しておくと便利。

> - \*OPTION\.TXTを%'list'に配置する必要があります。[sample](https://gist.github.com/tar80/f3e5860214e758834bdf0bd619060b0f)<BR>
>    - \*OPTION\.TXTは文字コードに注意。ユニコードならUTF16LBかUTF8BOMでないと文字化けします。<BR>
>    - ファイル名は、grep=GREPOPTION.TXT、rg=RGOPTION.TXT、jvgrep=JVGREPOPTION.TXTとしてください。
> - grepコマンドの変更にPPx Key Moduleを使用。
> - [LFexec.js]({{ site.baseurl }}{% post_url 21-01-24-script_lfexec %})、[compCode.js]({{ site.baseurl }}{% post_url 20-12-22-script_compcode %})、[commentSearch.js]({{ site.baseurl }}{% post_url 21-01-09-script_commentSearch %})が必要。
> - grep,rg,jvgrepを使用。

### 使い方
- cmdGrep.jsの初期設定(grepオプション、マークなしの選択対象)をする。
- `*script %'scr'%\cmdGrep.js,【grepの出力ファイルパス】,【初期選択grepコマンド】,【初期出力先:LF|PPv】,【オプションの再読込】`を実行。<BR>
  - コマンド毎のオプション登録はスクリプトの初回起動時にM\_grepに保存される。
  - 設定変更などで再度読み込む場合は、第四引数に`1`を指定。`2`を指定するとM\_grepを削除してコマンドを終了する。
- optionボタンからgrepの引数を変更できる。
- 編集中にCTRL+Gで、使用するgrepコマンドを変更できる。(KeyModule使用)

![sample]({{ site.baseurl }}{% link /public/img/grep.gif %})

#### 設定
```
;エイリアス
A_exec = {
scr  =  ;スクリプトをまとめておくディレクトリパス
list =  ;読み書きするリストをまとめておくディレクトリパス
}

;実行コマンド
;リストファイルのパスをエディタで開く
key or menu , *if %*js(PPx.Result = PPx.EntryMarkCount;) >= 4 %: %"確認"%Q"%*js(PPx.Result = PPx.EntryMarkCount;)エントリがマークされています%bn続行しますか？"
              %ME_editor

;PPvのカーソル行をエディタで開く ※キャレットモードのみ
KV_crt    = {
; gvimで開く場合
; %sp"grep"はPPvに設定される変数で中身は'1'。grep表示の判別用
; *topmostwindowはWindow Moduleのコマンド
key , *topmostwindow %N,0
      *string o,crtline=%*script(%'scr'%\compCode.js,"s","""")
      *if 0%sp"grep" %: gvim --remote-tab-silent +%*regexp("%so"crtline"","s/^[^:]*:(\d*):.*/$1") %*extract(C"%%FDN")%\%*regexp("%so"crtline"","s/(^[^:]*):.*/$1/") %: *stop
      gvim --remote-tab-silent +%L %FDC
}

;grep検索一行編集を呼ぶ
; スクリプトを実行すると、%si"output"(中身はLF|PPv)が設定され判別に利用できる。
key or menu , *string o,lf="%*temp(result.xgrep,name)"
              *string o,cmd=grep
              %Oi *script %'scr'%\cmdGrep.js,%so"lf",%so"cmd",LF
              *ifmatch %si"output",LF %: *ppc -r -single -mps -bootid:W %so"lf" -k *viewstyle -temp &GREP
              string i,output=

;コメント検索
key or menu , *script %'scr'%\commentSearch.js,filter

;一行編集
; 一行編集内では、
; %si"cmd"(中身はgrepコマンド)
; %si"output"(result出力先)
; %si"gopt"(grepオプション全体)
; %se"lock"(grepオプション固定部分), %se"add"(grepオプション可変部分)
; %se"flen"(%se"lock"の文字数), %se"blen"(%se"add"の文字数)
; %se"list"(補完リスト名)
; が利用できる。これらの変数はスクリプト終了時に削除されるが%si"output"のみ残る。
-|K_cmdGrepMap =
K_cmdGrepMap = {
^G , *ifmatch %si"cmd"%si"output",grepLF %: %M_grep,!rgLF
     *ifmatch %si"cmd"%si"output",rgLF %: %M_grep,!jvgrepLF
     *ifmatch %si"cmd"%si"output",jvgrepLF %: %M_grep,!grepPPv
     *ifmatch %si"cmd"%si"output",grepPPv %: %M_grep,!rgPPv
     *ifmatch %si"cmd"%si"output",rgPPv %: %M_grep,!jvgrepPPv
     *ifmatch %si"cmd"%si"output",jvgrepPPv %: %M_grep,!grepLF
     *setcaption [%si"output"] %si"cmd" %si"gopt" ※\=\\\\

;使用するgrepコマンドの数が多い場合は以下のほうが使い易いかも
;^G , *ifmatch !0,0%se"flen",grep %: %M_grep,g %: *setcaption [%si"output"] %si"cmd" %si"gopt" ※\=\\\\
}

;エディタ拡張子判別
E_editor  = {
* , *if 4==%*js(PPx.Result=PPx.DirectoryType;) %: *script %'scr'%\LFexec.js,【コマンド名】,【重複パスの実行】 %: *stop
    %"JMTE|Text edit"%Orib,editor %{ %FDC %}
}

;表示形式
MC_celS = {
GREP = M WF12 S1 f5 C200 s1
}

;ディレクトリ別設定(リストファイル)
XC_dset = {
listfile = B00000,-1,-1,19,-1,-1,B000000,B00000000000000000000000001,
}
```
<BR>
<script src="https://gist.github.com/tar80/57ef040143778808565d9decc4692219.js"></script>
