---
layout: post
title: テスト環境を構築するプラグイン
version: PPx191以降
date: 2023-06-24
comment: 投稿。
repository: tar80/ppm-test
categories: PPc PPe PPb @plugin
---

### 説明

おもに PPx 用 JScript の実行を補助するためのプラグインで、ppm-switchmenu を利用します。

- テストスクリプトの編集・実行をサポート
- JScript 用簡易ユニットテスト
- PPc 用ハイライトチェッカー

などの機能を提供します。

### 使い方

`ppm-test`のインストール後に、`*script %*getcust(S_ppm#plugins:ppm-switchmenu)\script\%*getcust(S_ppm#global:scripttype)\preset.js,test`を実行すると  
`S_ppm#global:editor` で ppm-switchmenu.cfg の編集が始まります。  
`M_ppmSwitch`に`&9:test`が追加されているのでショートカットキーやリストの位置などを調整して保存してください。  
`S_ppm#user:sw_cursor`は、メニュー表示時のカーソルを合わせる項目のショートカットキーです。  
`S_ppm#user:sw_check`には、メニューの頭(0)から数えたテストメニューの位置を指定します。

<BR>
設定後、`ppm-switchmenu`からコマンドを実行してください。

![sample]({{ site.baseurl }}{% link /public/img/test_menu.png %})

- `Edit` `Bench`は、それぞれ\<ppm\>\cache\xxx\test\xxx\以下の t.js、t_bench.js を`S_ppm#global:editor`で開きます。
- `Test` `Result`は、最後にメニューから実行したスクリプト(Edit または Bench)を実行します。  
  その際、Shift キーが押されていれば引数を指定できます。
- `Unit Test PPx`は、特定ディレクトリ配下の`XXX.js`に対するユニットテスト`t\utp_XXX.js`を実行し、
  結果を PPb[T] に出力します。`eval()`関数を使用しているため`const` `let`の値を読み取れず、
  strict モードのファイルも読み込めないため、実質 Jscript5.7 で記述されたスクリプト用です。
- `Highlight Checker`は、PPc のエントリリストに現在のハイライト情報を出力します。  
  コマンド実行時に Shift キーが押されていると PPe に色情報が出力され、編集後に保存`Ctrl+S`で
  ハイライト設定を更新できます。

#### Edit, Bench

test 関数を使用できます。test 関数はヘルプとテスト用スクリプト内に記載されています。  
スクリプトからの出力はログ窓に、なければログ窓と PPb[T] のどちらかを選択後に出力されます。

[動作例]({{ site.baseurl }}{% link /public/img/test_demo.webp %})

#### Unit Test PPx

`script\jscript\`に配置された`XXX.js`に対する`t\utp_XXX.js`を用意し、どちらかのファイルに
カーソルを合わせて実行します。  
テスト結果は色付けされて PPb[T] に出力されますが、コマンドプロンプトは標準では色付けされないため
Windows Terminal などの ansi に対応しているエミュレータ上で実行してください。  
コマンドプロンプトで実行したい場合は、ブランチを main から変更後 ppmtest.js の変数`has_color`を`false`にするか、
OS のシステム設定を変更してコマンドプロンプトを ansi に対応させる必要があります。  

[動作例]({{ site.baseurl }}{% link /public/img/test_unit-test.webp %})

<BR>
**ppmtest が提供する関数**

|                      関数                       | 内容                                                                                                                                                        |
| :---------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| describe(string:description, function:callback) | description にはテスト全体の説明を書きます。<BR>callback内に it、skip を記述します。                                                                         |
|      it(string:subject, function:callback)      | subject には一つのテストの説明を書きます。<BR>callback内に assert を記述します。                                                                             |
|     skip(string:subject, function:callback)     | subject には一つのテストの説明を書きます。<BR>通常 callback は実行されず成功を返しますが、<BR>変数`conv_skip_to_it`が`true`であれば callback を実行します。 |
|     assert.equal(any:expected, any:actual)      | expected と actual が同値であれば成功を返します。                                                                                                           |
|    assert.notEqual(any:expected, any:actual)    | expected と actual が同値でなければ成功を返します。                                                                                                         |
|   assert.do(any:expected, function:callback)    | callback を実行し、成功を返します。                                                                                                                         |

### Highlight Checker

![動作例]({{ site.baseurl }}{% link /public/img/test_hl.webp %})
