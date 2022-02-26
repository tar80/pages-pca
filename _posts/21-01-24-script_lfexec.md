---
layout: post
title: LFexec.js
version: PPx181以降
date: 2022-02-26
comment: git grep用のコードを追加
categories: Script
---

### 説明

listfileのマークした行情報を一行ずつコマンドに渡します。その際、ShortName項目を数値として取得(number)できます。

- 呼び出すコマンド内容は、lfexec\_cmd\.js内に記載。
- 変数(number)はShortName項目を数値として解釈した値。
  - ShortNameが数値でない場合は1を返します。(※cmdGrep\.jsのresult用途)

> - スクリプトから呼び出すコマンドは<span style="color:red;">Function関数</span>で読み込んでいます。

### 使い方

- lfexec\_cmd\.jsのcmd[]内に実行するコマンドを記述する。変数は、ファイルパス= `path`, ショートネーム= `shortname`,
行数= `number`,重複パス実行= `duplicate`, 検索語= `search_word`が指定できる。詳細はスクリプト内のサンプルを参照。
- \*script %'scr'%\LFexec.js,【コマンド名】,【重複パスの実行】で実行。第二引数は、`0`か`1`で指定。
指定しない場合は0が代入される。1ならパスの重複に関わらず全マークを処理する。

> git grepでコミットを対象とした際には検索語が`コミット名@@@検索語`となる。後付けで無理矢理実装したので
不具合があるかもしれません。詳細はLFexec\_cmd\.js参照

#### 設定

```text
A_exec = {
scr     = ;スクリプトをまとめておくディレクトリパス
;gvim    = ;サンプルコマンドgvim.exeのパス
}
```

<BR>
<script src="https://gist.github.com/tar80/326a561b4ab0b54579c825b44d5ece59.js"></script>
