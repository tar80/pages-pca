---
layout: post
title: Ralphaを使って画像処理
version: PPx181以降
date: 2021-09-06
comment: コマンドを微調整
categories: PPc
---
### 説明
画像処理ソフトRalpha Image ResizerとPPxの連携。
- 最初にRalpha側で画像加工と出力の処理を保存しておきます。
- 次にPPcからコマンドライン経由でRalphaにファイルを投げて処理を自動化。
- 画像処理の設定はINIで管理されているのでいくつでも作れますが、設定毎に
PPxのコマンド登録するのは大変なので、一つのコマンドで運用できるようにしています。

> - 設定ファイルはわかりやすい名前を付けるべし。<BR>
> - ralpha\iniディレクトリのパスと実行ファイルのパスは環境に合わせて設定するべし。<BR>

### 使い方
 - 処理したい画像を選択し、コマンドを実行。
 - 一行編集が出てくるので補完リストから設定を選択する。

#### 設定
コンテキストメニュー
```clean
&RalphaCMD = *string o,inipath=%*name(C,"%*input("【ralpha\iniディレクトリのパス】%\" -title:"設定ファイルの選択" -select:l -k: *completelist)")
             ralpha.exe /ini=%so"inipath" %#FDC
```
