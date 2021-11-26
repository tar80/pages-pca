---
layout: post
title: polyfill.js
verions: PPx181以降
date: 2021-11-01
comment: 投稿。
categories: Script
---
### 説明
Jscript用のポリフィルローダー。<BR>
ポリフィルをまとめたスクリプトをモジュールとして読み込みます。<BR>
> - <span style="color:red;">Function関数を使っている</span>ので非推奨ですが、アイデアとして残しておきます。
> - PPx.Resultで読み込んでいるので文字数制限があります。※文字数オーバーはエラー。
> - PPxで読める文字コードなのは当然として、スクリプト間の文字コードを合わせておく必要があります。

### 使い方
1. %'scr'\module以下にpf_○○.jsを配置。
1. MDNなどから必要なポリフィルを探してきてモジュールスクリプト内`PPx.Result = function() {}`の中に追記。
1. ポリフィルを使いたいスクリプトの初めに下記コードを書き込む。<BR>

```javascript
Function('return ' + PPx.Extract("%*script(%'scr'%\\module\\pf_○○.js)") + '()')()
```

#### :wrench:設定
```clean
A_exec = {
scr     = ;スクリプトをまとめておくディレクトリパス
}
```
<BR>
<script src="https://gist.github.com/tar80/96a8206f8efad3b36384c73da755df9f.js"></script>
