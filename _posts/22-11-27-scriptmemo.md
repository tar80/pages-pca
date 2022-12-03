---
layout: post
title: PPxスクリプトの覚書
version: PPXSCR18R2以降
date: 2022-11-27
comment: 投稿。
categories: Script
---

### 説明

PPx用JScriptを書くときのtips。自分が忘れないように書き留め。  

#### 別スレッドへのスイッチ

`%K`と`%K[ID]`では実行される順序が異なる。  
下はエントリリスト更新後に"dummy-entry"を挿入するコードだが、IDありの場合  
想定通りの動作にはならず"dummy-entry"挿入後にリストが更新される。  

```
// IDなし
PPx.Execute('%K"@F5"');
PPx.EntryInsert(0, 'dummy-entry');

// IDあり
PPx.Execute('%KC"@F5"');
PPx.EntryInsert(0, 'dummy-entry');
```

このようなケースでは、`*wait 0,n`を挟むことで実行する順序を制御できる。  

```
PPx.Execute('%KC"@F5"');
PPx.Execute('*wait 0,1');
PPx.EntryInsert(0, 'dummy-entry');
```

PPXSCR.TXTでは`PPx.Sleep(0)`で説明されているが、\*waitの仕様が変わったためか  
`PPx.Sleep(0)`および`*wait 0`ではスレッドは変更されない。  

#### 別PPx上でのコマンドの終了コード取得

PPtrayから実行したスクリプトで、PPc[A]上でのコマンド終了コードを得るには以下のようにする。  

```
// 戻り値は、実行="1"、キャンセル="0"
// %*choiceのオプション"-type"によって値は変化する
PPx.Extract('%*extract(CA,"%%*choice()")');

// 戻り値は、実行="1"、キャンセル=""(空文字)
PPx.Extract('%*extract(CA,"*file%%:1")');
```

コマンド`%Q`は`PPx.Extract()`では実行されないので`%*choice()`を使う。  
`%*extract()`は、実行(PPx.Execute)系のコマンドを挟んでも最終的に文字を返せば動作するようだ。  
`*ppcfile`など別スレッドで処理されるコマンドは終了コードを取得できない。

#### 実行速度の違い

自分で計測して判明している明らかな実行速度の違い。  
※ミリ秒基準の差。体感速度の違いはないに等しい。

```
// 速い
PPx.Extract('%*extract("コマンド")');
// 遅い
PPx.Extract('%*extract([ID],"コマンド")');
```

IDの指定如何によって10倍前後の速度差が出る。これはIDの検索にかかる時間と思われるので、
他のケースでもID検索が発生する場合には同様の速度差が発生するかもしれない。  
ただし、`%K`と`%K[ID]`の速度差は誤差の範囲だったため、Execute系では差はなく
Extract系で差が出ていると推測される。

