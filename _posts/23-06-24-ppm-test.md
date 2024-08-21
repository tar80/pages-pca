---
layout: post
title: テスト環境を構築するプラグイン
version: PPx191以降
date: 2024-08-21
comment: 改稿。
repository: tar80/ppm-test
categories: PPc PPe PPb @plugin
---

### 説明

主にPPx用JavaScriptの実行を補助するためのプラグインでppm-switchmenuを利用します。

- テストスクリプトの編集・実行をサポート
- PPc 用ハイライトチェッカー

などの機能を提供します。  
<BR>
ppmの新仕様に対応しました。内容が少しだけ更新されています。  

- switchmenu用のtest.cfgの内容を更新。以下を実行して適用してください。  
`switchmenu -> 1:ppm -> 差分比較(C) -> プリセットメニュー設定ファイル(M)`を実行  
`test.cfg`を選択し、差分を適用。
- ユニットテストはできがよくないので削除しました。  
それなりのものを作ろうと思うと知識と時間が足りないので、新らしく作り直すことは考えていません。  

### 使い方

`ppm-test`のインストール後に、`*script %sgu'ppmrepo'\ppm-switchmenu\dist\addMenuItem.js,test`
を実行すると`S_ppm#global:editor`で、ppm-switchmenu.cfg の編集が始まります。  
`M_ppmSwitch`に`&n:test`が追加されているのでショートカットキーやリストの位置などを調整して保存してください。  
`S_ppm#user:sw_cursor`は、メニュー表示時のカーソルを合わせる項目のショートカットキーです。  
`S_ppm#user:sw_check`には、メニューの頭(0)から数えたテストメニューの位置を指定します。

<BR>
設定後、`ppm-switchmenu`からコマンドを実行してください。

![sample]({{ site.baseurl }}{% link /public/img/test_menu.png %})

**Edit, Bench**  
それぞれ`%sgu'ppmcache'\userscript`以下の`quickRun.js`、`quickBench.js`を  
`S_ppm#global:editor`で開きます。  
**Test, Result**  
直近のEdit、またはBenchで編集したquickXXX.jsを実行します。  
選択時にShiftキーが押されていれば引数を指定できます。  
**Highlight Checker**  
PPc のエントリリストに現在のハイライト情報を出力します。  
選択時にShiftキーが押されていればPPeに色情報が出力されます。  
編集後に保存`Ctrl+S`でハイライト設定を更新できます。

#### Edit, Bench

`fso`, `ppm`, `test`オブジェクトを使用できます。  
**fso**は`Scripting.FileSystemObject`です。  
**ppm**はPPxオブジェクトのラッパーです。詳細はppmdevのドキュメントを見るか、  
userscript/_testHelper.jsを直接参照してください。  
**test**は以下のメソッドを提供します。  

- **print(ppxid, value)** PPx.report()を実行します。  
  `ppxid`は実行されるPPxのIDです。マクロの値などが変動します。  
  `value`は出力される値です。文字列、数値、配列、オブジェクトを指定できます。  
  `Type: 値の型`、`Value: 値`の形式で出力されます。  
- **tableItem(key, value)** テーブル項目です。  
  `key`はテーブルのプロパティ名、`value`にはコマンドラインを記述します。
- **table(att, callback)** 一時的なテーブルを設定し、実行します。  
  `att`はテーブルの属性です。`E`(拡張子)、`M`(メニュー)'、`K`(キー)のいずれかを
  指定します。  
  `callback`内には`tableItem()`を記述します。
- **measure(subject, callback)** ベンチマークで実行するテストコードです。  
  `subject`にはテストコードの件名、`callback`内にテストコードを記述します。
- **benchmark(desc, count, session)** ベンチマークを実行します。  
  `desc`にはベンチマークの説明、`count`はテストコードの実行回数、`session`はコールバック関数です。  
  `session`内に`measure()`を記述します。  

スクリプトからの出力はログ窓、またはPPb\[T]のどちらかに出力されます。

[動作例]({{ site.baseurl }}{% link /public/img/test_demo.webp %})

<!-- #### Unit Test PPx -->
<!---->
<!-- `script\jscript\`に配置された`XXX.js`に対する`t\utp_XXX.js`を用意し、どちらかのファイルに -->
<!-- カーソルを合わせて実行します。   -->
<!-- テスト結果は色付けされて PPb[T] に出力されますが、コマンドプロンプトは標準では色付けされないため -->
<!-- Windows Terminal などの ansi に対応しているエミュレータ上で実行してください。   -->
<!-- コマンドプロンプトで実行したい場合は、ブランチを main から変更後 ppmtest.js の変数`has_color`を`false`にするか、 -->
<!-- OS のシステム設定を変更してコマンドプロンプトを ansi に対応させる必要があります。 -->
<!---->
<!-- [動作例]({{ site.baseurl }}{% link /public/img/test_unit-test.webp %}) -->
<!---->
<!-- <BR> -->
<!-- **ppmtest が提供する関数** -->
<!---->
<!-- | 関数名                                          | 内容                                                                                                                                                        | -->
<!-- | :---------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------- | -->
<!-- | describe(string:description, function:callback) | description にはテスト全体の説明を書きます。<BR>callback 内に it、skip を記述します。                                                                       | -->
<!-- | it(string:subject, function:callback)           | subject には一つのテストの説明を書きます。<BR>callback 内に assert を記述します。                                                                           | -->
<!-- | skip(string:subject, function:callback)         | subject には一つのテストの説明を書きます。<BR>通常 callback は実行されず成功を返しますが、<BR>変数`conv_skip_to_it`が`true`であれば callback を実行します。 | -->
<!-- | assert.equal(any:expected, any:actual)          | expected と actual が同値であれば成功を返します。                                                                                                           | -->
<!-- | assert.notEqual(any:expected, any:actual)       | expected と actual が同値でなければ成功を返します。                                                                                                         | -->
<!-- | assert.do(any:expected, function:callback)      | callback を実行し、成功を返します。                                                                                                                         | -->

### Highlight Checker

![動作例]({{ site.baseurl }}{% link /public/img/test_hl.webp %})
