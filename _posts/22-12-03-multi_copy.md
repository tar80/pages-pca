---
layout: post
title: マルチコピー・削除
version: PPx187以降
date: 2022-12-03
comment: 投稿。
repository: tar80/ppm-fileoperation
categories: PPc @plugin
---

### 説明

マークしたエントリを複数のパスに対して一括してコピー・削除します。  

### 使い方

メニューからコマンドを実行すると一行編集が表示されるので、コピーしたいディレクトリのパスを空白で区切って入力します。
このとき、`Ctrl+S`で編集中テキストを、`Ctrl+O`でPPeを使って候補を追加できます。

マルチコピーでは送り先に同名ファイルがあった場合スキップされますが、メニューから
コマンドを実行する際にシフトキーが押されていると上書き処理に変更されます。これはコピー時のみ
有効であり、リンク作成時には適用されません。  
<BR>
![sample]({{ site.baseurl }}{% link /public/img/multi_copy.gif %})



