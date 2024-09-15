---
layout: post
title: Batを使ってPPvで構文ハイライト
version: PPx191+2以降
date: 2024-09-15
comment: Tipsを追加。
repository: tar80/ppm-view
categories: PPc PPv @plugin
---

### 説明

PPvは非常に多機能なファイルビューアでansiエスケープシーケンスにも対応しています。

つまり[Bat](https://github.com/sharkdp/bat)で構文ハイライトできたりします。  
![sample]({{ site.baseurl }}{% link /public/img/view_bat.png %})
<BR>
ただしBatはUTF-8とBOM付きUnicodeしか読めませんし、PPvとの連携も簡単ではないので
プラグインにしました。  
文字エンコードの判定は自動的に行われますが、BomなしUnicodeの判別はできないので
Batを通さずに出力されます。この場合でも、一度PPvに表示したうえでの再更新時には
文字コードの判別が可能です。起動後に構文ハイライトを遅延読み込みすることもできます。  
またScoopでNkfやBatをインストールしている場合など、実行時にDos窓がちらつく場合は
エイリアスにpath\to\nkf.exe、path\to\bat.exeを登録すれば解決するかもしれません。

### 使い方

ppm-viewをインストールして設定します。  
標準では以下のキーで構文ハイライトが適用されます。

| key                   | command                              |
| :-------------------- | :----------------------------------- |
| (PPc) N               | 拡張子判別ビューア起動               |
| (PPv) CTRL+SHIFT+UP   | 前のファイル(構文ハイライト)         |
| (PPv) CTRL+SHIFT+DOWN | 次のファイル (構文ハイライト)        |
| (PPv) F5              | 再読み込み(構文ハイライト有無を考慮) |
| (PPv) SHIFT+F5        | 再読み込み(構文ハイライト)           |

### Tips

Batはカラーテーマを変更できます。使用できるテーマの確認にはリストオプションを使用します。
`bat --list-themes`で表示されるサンプルから好みのものを選んでオプションに設定すれば
テーマが適用されます。  
ppm-viewでは、`[section]`内でLaunchPPv.jsの第6引数と、`?stxbatoption`で再読み込み時の
オプションを設定できます。注意点として、名前の大/小文字・記号は正確でなければならないこと、
スペースを含む場合は`"`で括らなければならないこと、があります。  
`--theme="Visual Studio Dark+"`  
さらに全体を括る必要がある場合はエスケープが必要です。  
`"--theme=""Visual Studio Dark+"""`  
<BR>
すべてのテーマを確認しましたが、PPv上で映えるテーマは標準の`Monokai`だと思いました。  
個人的には、`1337`,`Coldark-Dark`,`Dracula`,`Sublime Snazzy`,`base16-256`なども
よいと思いますが、見えづらい色があったり数字が背景強調になっていたりと、いまひとつだったので
テーマを拡張して`Catppuccin`を使用しています。`Latte`以外は見易くなってよい感じです。  
テーマは有名所なら対応している可能性があるので、好みのものがあれば調べてみるとよいでしょう。  
