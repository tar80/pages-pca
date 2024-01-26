---
layout: post
title: パス補完一行編集
version: PPx194以降
date: 2024-01-26
comment: 投稿。
repository: tar80/ppm-comppath
categories: PPc @plugin
---

### 説明

この記事は[fenrirscanを用いたパス補完]({{ site.baseurl }}{% post_url/19-11-07-complate_path %})と
[zoxideと連携]({{ site.baseurl }}{% post_url/22-07-22-zoxide  %})をまとめたものです。

<BR>
パス補完一行編集(ppm-comppath)には以下のような機能があります。

- キー操作による編集中のパスの上方、下方への補完
  > 上方への補完は[PPxをランチャとして使う⑦ステップイン・アウト](http://hoehoetukasa.blogspot.com/2018/11/ppx_7.html)を参考にさせて頂きました。
- パス入力の途中でENTERを押したときに補完リストの第一候補を選択
  > ヒストリに入力途中の履歴を残さないようにしています。
- スペースを含むパスにある程度対応。
- マクロ文字(%0など)、auxパスに対応。
- コマンドを含む文字列にある程度対応。
- fenrirScanで生成したパスリストをPPXUPATH.TXTに出力
- everythingとの連携
- zoxideとの連携

### 使い方

ppm-comppathをインストールして設定します。

- 一行編集起動時はカレントディレクトリが初期値として入力されます。  
- `CTRL+L`キーで同階層のディレクトリパスを補完できます。  
- 補完候補はPPxの表示履歴とPPXUPATH.TXTから抽出され、必要に応じて
- Everythingのインデックスをトグルできます。  
- パスの上方、下方補完はパス移動以外の一行編集上でも使用できます。

#### fenrirScan

fenrirScanはルールに従ってパスリストを作成するツールです。レジストリは使用しません。  
ppm-comppathではPPXUPATH.TXTの更新に利用しています。
fenrirScanの関連ファイルをPPxのインストールディレクトリに配置する必要がありますが、
ppm-comppath設定内`[execute]`内、該当箇所のコメントを外せば必要なファイルを自動でセットすることもできます。
このときダウンロードしたfenrirの書庫ファイルは%sgu'ppmarch'配下に保存されます。  
fenrirScanのルールはScanRule.iniに記述してfenrirScan.exeと同じパスにshift-jis形式で保存して配置します。
他形式の場合は正常にファイル名を認識できません。ルールの記述方法はfenrirScan.txtを参照してください。  
ルールはパス移動一行編集上で呼び出せるメニュー(`CTRL+S`)からも登録できます。このメニューから登録したとき
ScanRule.iniがない場合は自動で生成されます。

<BR>
![sample]({{ site.baseurl }}{% link /public/img/comppath1.webp %})

#### zoxide

zoxideは移動したことのあるパスを独自にレーティングし、より移動頻度の高いパスを検出するためのツールです。レジストリは使用しません。  
ppm-comppathではコンソール上にFZF経由で起動する方法と、一行編集上でクエリを利用する方法を用意しています。

![sample]({{ site.baseurl }}{% link /public/img/comppath2.webp %})

#### キーバインド

PPc

|  KEY   | COMMAND                             |
| :----: | :---------------------------------- |
|   @    | 一行編集(パス移動用)                |
| CTRL+@ | `zoxide query -i`をコンソールで実行 |

<BR>
一行編集

|     KEY     | COMMAND                                         |
| :---------: | :---------------------------------------------- |
|   CTRL+H    | 編集中のディレクトリパスを一つ上へ補完する      |
|   CTRL+L    | 編集中のディレクトリパスを下層へ補完する        |
|   CTRL+N    | 次の候補                                        |
|   CTRL+P    | 前の候補                                        |
|   CTRL+S    | fenrirScanメニューを表示                        |
|   CTRL+Z    | 入力文字をzoxideに送り、戻り値で入力文字を置換  |
|   CTRL+@    | everythingインデックス on/offのトグル           |
|    ENTER    | パス入力途中でENTERした時に補完候補一行目を選択 |
| SHIFT+ENTER | 編集したパスを反対窓で開く                      |
| CTRL+ENTER  | 入力文字をzoxideに送り、戻り値のパスへ移動      |
