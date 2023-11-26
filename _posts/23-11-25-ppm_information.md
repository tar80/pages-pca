---
layout: post
title: ppm information
version: 194以降
date: 2023-11-25
comment: 投稿。
repository: tar80/ppx-plugin-manager
categories: '@plugin'
---

### 説明

ppm v0.93をリリースしました。  
このバージョンから新システムが導入され、旧設定、新設定の二種類の設定が混在することになります。  
一から作り直したため移行するにはいくつかの手順を踏む必要があります。十分にテストができているとは  
言い難いので、一度アンインストールしてから再度インストールすることを推奨します。  
アンインストールする場合は、以前の設定データが`S_ppm#global:home`に残っているので別の場所に移動するか、  
削除してからインストールを実行してください

#### 旧システムから移行する場合

ppm v0.93への更新後、`*ppmUpdate`の実行、または`*script ppm\dist\installer.js`の直接実行で新システムへの  
移行確認ダイアログが表示されます。

1. 最初にgitのインストールディレクトリが確認されます。gitのパスを検知できなかった場合は一行編集が表示されますので、
   gitのインストールディレクトリパスを入力してください。  
   このパスはgit/usr/bin/配下のunixコマンドへのアクセスに使います。

2. 次に再インストール(旧設定の上書き)の確認ダイアログが表示されるので決定します。  
   ppmの設定が上書きされますが、プラグインごとの設定はそのまま残ります。

3. ppmをインストールするディレクトリパスを入力します。  
   パスを変更したい場合は新らしいパスを指定してください。現在のパスから新らしいパスへ
   ppmのインストールディレクトリをまるごと移動します。複数のプロファイルがあり別々の場所にインストールしたい場合は、
   それぞれの`S_ppm#global:ppm\cache\xxx`に、旧`cache\xxx`を上書きしてください。

4. ここまでの操作で本体の更新は完了します。`*ppmXXX`コマンドも新らしい内容に更新されていますので、
   `*ppmCompare`を実行して該当部分の差分を適用してください。旧版の`*ppmXXX`コマンドが実行された場合、
   新らしいコマンドが走るように処理していますが、漏れがあるかもしれません。

以上の設定を終えれば新システムへの移行が完了します。  
また、_pluginlistに新らしいルールを追加したので、`%sgu'ppmcache'\list\_pluginlist`に、  
`%sgu'ppm'\sheet\_pluginlist`の差分を適用してください。  
<BR>

新システムでは、いくつかの変数やファイル類が追加され、逆に使わなくなった変数やファイルもあります。  
v0.92までの、すべてのJScriptも削除対象です。これらは互換性のために残してありますがv1.0.0までに削除します。  
操作は今までと大差ありませんが内部は別物です。細かいところがいろいろ変わり、ユーザー設定の自由度も  
あがっています。詳細はヘルプを確認してください。

#### ppm-switchmenu

本体と同時に`ppm-switchmenu`も更新されています。  
こちらも大きく変更されているため、新らしいメニューへ変更する手順を説明します。

1. `*ppmSetDefault`を実行し、起動した一行編集に`ppm-switchmenu`を入力します。  
   入力途中で`Enter`キーを押した場合、補完リストの最初の候補が選択されます。
   > `KC_main:T`(PPcの"T"キー)が上書きされるので注意してください。
   > ppmをインストールするまえのPPxの設定は、`%sgu'ppmcache'\backup\_iniital.cfg`に保存されています。

2. PPc上で`T`キーを入力するとスイッチメニューが起動するので、`差分比較(C)` >
  `プラグイン設定ファイル(P)` を選択し、一行編集に"ppm-switchmenu"を入力します。

3. `S_ppm#user:compare`に指定したツールで差分が開きます。全体が変更されているため、  
   一度すべてコピーしたあとに必要な箇所を編集してください。

4. 一行編集から`*ppmSet`を実行し、`ppm-switchmenu`と入力して設定をPPxに適用します。

### v0.93の大きな変更点

- v0.92まではPPxScriptModuleの使用ライブラリにJScript.dllと、Chakra.dllを選択
  できましたが、v0.93以降はPPxが使用できる4つのJavaScriptライブラリ(JScript.dll、
  JScript9.dll、Chakra.dll、ClearScriptV8.dll)のどれでも動作するようになりました。
  ただしプラグイン側は未対応で今後の対応になります。  
- `*ppmXXX`コマンドは引数を記述する必要がありましたが、コマンド内で一行編集を
  呼び出すように変更しました。  
  また、いくつかのコマンド名は変更されました。  
  `*ppmUninstall` -> `*ppmSystemUninstall`  
  `*ppmSet`,`*ppmSetup` -> `*ppmSet`  
  `*ppmDefault` -> `*ppmSetDefault`  
  `*ppmLoad` -> `*ppmLoadCfg`  
  新規追加コマンド `*ppmUsed`  
  削除されたコマンド `*ppmBackup` `*ppmUsejs`
- プラグインリストで、プラグインの使用ブランチ・コミットを設定できるようにしました。
- 設定ファイルの`[section]`内で、文字列を置換できるようにしました。
- 設定ファイルに、適用時のみ実行される項目`[execute], [endexecute]`を追加しました。
- `script`,`lib`,`module`ディレクトリは廃止、`dist`ディレクトリに統一されました。

#### 補足

ppm v0.93から、npm workspace上に開発環境を整えました。以前はとりあえず動けばよい
という方針であまり考えなく作っていましたが、今後は少しまともな環境で開発できます。  
大きな違いはJScriptで書いていたスクリプトをTypeScriptで書けるようになったことです。  
PPxで利用できるJavaScriptは大きく分けてJScript5.7(ES3相当)、JScript9(ES5相当)、
Chakra(ES6相当)、ClearScript(V8)の四種類があり、それぞれのスクリプトを共用する
ことは難しかったのですが、TypeScriptで書いてES3相当にトランスパイルすることで、
どのライブラリでも同様に動作するようにしました。  
今まで無理矢理実装していたあれやこれやを無理なく実装でき、Jestを使ったテストも
書けるので潜在的なバグを減らせ、スクリプトの実行速度も結果的には上がる見込みです。  
なによりESNEXT相当の構文やインテリセンスを使えるため記述面での負担が減りました。  
<BR>
開発環境はリポジトリ[ppmdev](https://github.com/tar80/ppmdev)です。
`npm install`すれば簡単に環境を作れるので興味があれば試してみてください。  
TypeScriptでは、VsCodeなどのLSPに対応したエディタであればWScript・PPxオブジェクト
の型も読めます。  
動作にはNodeJsが必要です。
