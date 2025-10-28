---
layout: post
title: Obsidianと連携
version: PPx203以降
date: 2025-06-20
comment: 投稿。
repository: 
categories: PPc Script
---

### 説明

だらだら使っていたObsidianがいつの間にかAIと連携して超絶web clippingツールに  
なっていたのでPPxとの連携を考えてみます。  

#### 一行編集からの実行

最近PPxは`URI:`の呼び出しに対応されました。  
こんな感じでPPXUCMD.TXTに追加しておくとvault指定で呼び出せて便利です。

```plain
;<URI>; obsidian://open?vault=my-vault
;<URI>; obsidian://open?vault=clipping
```

URI:はパス移動ではなく、コマンド実行で指定します。  

#### vault管理下のファイルはObsidianで開く

Markdownの編集時に通常は`editor`、vault配下は`Obsidian`でファイルを開けるよう  
にします。以下のように`isObsidianVault.js`を拡張子判別に登録します。  
<BR>
設定例

```text
E_editor = {
md ,*if(1==%*script("path\to\isObsidianVault.js"))%:obsidian://open?path=%FDC%:*stop
   editor
}
```

<BR>
<script src="https://gist.github.com/tar80/0eb859b3ad9233d3c6ae02caa17aca8c.js"></script>

#### web clipping

本題です。PPxとはまったく関係ないですが自分の運用方法を解説します。  
`obsidian web clipping`と検索してまず出てくるのはObsidian公式が公開している  
Obsidian Web Clipperというブラウザ版エクステンションです。これはページを  
まるごとMarkdownとしてローカルにクリップしてくれるツールです。  
使い方はたくさんの紹介記事が見つかるのでそれを参考にしてください。  
<BR>
このエクステンションは現在見ているページを保存するには適していますが、  
いくつものサイトを巡回して情報を収集する用途には向いていません。  
私は次のような方法でクリッピングしています。

1. 最初にブラウザと連携できるブックマークサービスに登録します。 `あとで読む`  
   機能がついているものがよいです。今回は、 はてなブックマークで説明します。  
1. ブラウザで巡回中に気になるページがあれば片っ端から`あとで読む`に登録します。  
   このとき、なにが気になったのかコメントをつけておくと後で仕分けしやすいです。  

   > はてなブックマークは目を通したページを自分で削除しないと溜まって  
   > いくのが厄介ですが、今どきブックマークレットをサポートしていて  
   > ブラウザエクステンションが不要なのはいい点です。

1. ObsidianからWebViewを開きます。WebViewのスタ－トページは、はてなブックマークの  
  `あとで読む`のページにしておくとよいでしょう。
1. 適当に`あとで読む`を巡回します。Obsidianに別のタブを2画面で開いておいて、  
   気になった記事や残しておきたい情報をコピペしていきます。
1. 最後にSmart Composerを使ってうまいことまとめてもらいます。プロンプトは  
   "重複した内容の削除。話題ごとにカテゴライズしてソート。カテゴリごとにヘッダをつける"  
   のような指示でいい感じにまとめてくれます。

   > Smart ComposerはGemini apiの無料プランで使っていますが十分な性能です。  
   > 勉強してRAGを使いこなせばPPxのヘルプから必要なコマンドを拾ったり、  
   > 簡単なJScriptを書かせることもできそうです。すごい時代になりましたね。
