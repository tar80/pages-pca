---
layout: post
title: fenrirscanを用いたパス補完
version: PPx181+3以降
date: 2021-11-28
comment: selStr.js -> setSel.jsに変更。補完リストの読み込み方法を調整。
categories: PPc
---
### 説明
PPc一行編集でのパス補完サポート。
- fenrirscan.exeで生成したパスリストを使ったインデックス検索。
  > - fenrirscan.exeはppc.exeのあるディレクトリに置いてください。
  > - パスリストにはPPXUPATH.TXTを使用。fenrirscan.exeで生成後、PPXFPATH.TXTの内容が追記されます。
- キー操作でパスを昇降可能。
  > 上方への補完は[PPxをランチャとして使う⑦ステップイン・アウト](http://hoehoetukasa.blogspot.com/2018/11/ppx_7.html)を参考にしました。
- コマンドランチャのように入力途中でENTERしても補完。
  > ヒストリに履歴を残さないように%\*input(... '-mode:e -k \*completelist -module:off -history:d')としています。
- スペースを含むパスにある程度対応。
- コマンドを含む文字列にある程度対応。
- マクロ文字(%0など)、auxパスに対応。
- TAB補完を有効にするにはX\_ltabの第一項目を1にする、もしくは\*editmode -tabkey:onを設定してください。<BR>
  > 一行編集上にalt+移動候補キーが存在しない場合、X\_ltabの値に関わらず
  TAB補完が有効になるようです。普段はmigemo不使用(0,2)、一時的にmigemo使用(1,4-6)するような場合に
  以下のコマンドが利用できます。<BR>
   ```
   *linecust ltab,K_lied:FIRSTEVENT,*setcust X_ltab=%*getcust(X_ltab) %%: *linecust ltab,K_lied:FIRSTEVENT,
   *setcust X_ltab=1,4
   ```

> - コマンド内で[setSel.js]({{ site.baseurl }}{% post_url 2020-12-26-script_setSel %})、[compCode.js]({{ site.baseurl }}{% post_url 20-12-22-script_compcode %})、[result.js]({{ site.baseurl }}{% post_url 20-12-18-script_result %})を使っています。
> - X\_fdir=1, X\_flst≧3 ,X\_ltab≧1 で使うことを想定。
> - コードをダウンロードして使う場合、fenrirscan.iniは文字コードを変換する
  必要があります。元からあるiniファイルにコピペしたほうが楽かも。

### 使いかた
補完の邪魔になることが多いので標準でEverything Search Moduleは不使用。
有効にするには`-module:off`の箇所を`-module:on`に変更する。

<BR>
![sample]({{ site.baseurl }}{% link /public/img/complete_path.gif %})

#### キーバインド
PPc

| KEY | COMMAND |
|:-:|:-|
| @ | 一行編集(パス移動用) |
| SHIFT+@ | 一行編集(反対窓を非アクティブで開く) |

<BR>

一行編集

| KEY | COMMAND |
|:-:|:-|
| F12 | パスリストの更新(fenrirscan) |
| CTRL+H | 編集中のディレクトリパスを一つ上のパスにする |
| CTRL+L | 編集中のディレクトリパスを下層へ補完する |
| CTRL+N | 次の候補 |
| CTRL+P | 前の候補 |
| CTRL+S | 編集中のパスをScanRule.iniに追加 |
| ENTER | パス入力途中でENTERした時に補完候補一行目を実行 |


#### 設定

```clean
;エイリアス
A_exec = {
scr = ;スクリプト親ディレクトリパス
}

;通常時のTAB補完設定 0,2、又は0,4-6に設定しておくと使いやすいと思います
X_ltab = 0,2

;PPcキーバインド
KC_main = {
'@'    , *string o,path=%*input("%*name(DN,"%FDVN")" -title:"Jumppath.." -mode:e -k *editmode -allkey %%: *completelist -module:off -history:d %%: *mapkey use,K_liedMAP)
         *jumppath %sgo"path"
\V_HC0 , *string o,path=%*input("%*name(DN,"%FDVN")" -title:"Jumppath..OP" -mode:e -k *editmode -allkey %%: *completelist -module:off -history:d %%: *mapkey use,K_liedMAP)
         *if 2 > %*js(PPx.Result=PPx.Pane.count;) %: *ppc -noactive -bootid:~ %sgo"path" %: *stop
         *execute ~,*linemessage %sgo"path"
}
;一行編集キーバインド
K_lied = {
^H , *string o,complist=%'temp'%\comp.txt
     *replace %*script(%'scr'%\compPath.js,%so"complist")
     %K"@END"
^L , *if 0%*editprop(list) %: *completelist -close
     *ifmatch "o:e,a:d+","%*regexp("%*script(%'scr'%\compCode.js,"e","""")","s/.*[\s,](.*)/$1/")" %: *replace "%*edittext%\"
     %K"@END@TAB"
;    *script %'scr'%\setSel.js,"(.*\\(?!$))(.*)"
;     末尾選択状態にするなら↑コメントを外す
^N = @DOWN
^P = @UP
}

K_liedMAP = {
ENTER , *string o,path=%*extract("%*edittext")
        *ifmatch /^aux:.*/,%so"path" %: %K"@ENTER" %: *stop
        *if 10==%*editprop(list)%*result(exists,%so"path") %: %K"@DOWN @ENTER" %: *stop
        %K"@ENTER"
F12   , %Oq *run -noactive -d:%0 fenrirScan.exe %& %Obd type %0%\PPXFPATH.TXT >> %0%\PPXUPATH.TXT %& *completelist /reload
^S    , *linemessage [Add] %*edittext %: %Obd echo +%*edittext>> %0%\ScanRule.ini
}
```
<BR>
<script src="https://gist.github.com/tar80/bb366d370a8a25ee903b3163d42a82f1.js"></script>
