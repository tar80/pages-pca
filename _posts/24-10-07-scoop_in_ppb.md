---
layout: post
title: PPbでscoopを実行するとcmd.exeが起動する
version: PPx199+1
date: 2024-10-07
comment: 投稿。
repository: 
categories: PPc PPb
---

### 説明

タイトル通りです。  
`scoop status`のように実行しても結果が表示されると同時に終了するので困ってしまいます。  

##### なぜなのか？

以前に[こちら]({{ site.baseurl }}{% post_url 24-08-12-scoop_shims %})で説明したとおり、
`scoop`コマンドは実行ファイルを指すものではなく、  
エイリアスであることが関係していそうです。  
<BR>
PPbで`scoop`を実行すると大まかに次のような処理が発生します。  

1. `%PATH%\scoop`を探す
1. `path\to\Scoop\shims\`に`scoop`を発見
1. cmd.exeで`path\to\Scoop\shims\scoop`を実行

PPbは渡されたファイルパスをOS側の拡張子判別で処理しているようです。  
E_crの拡張子判別はPPcから渡されたパスを対象としているのでこの場合は対象になりません。  

##### ではPPb上に scoop status は出力できないのか？

path\to\Scoop\shims配下には3つのscoopファイルがあります。  

```clean
 Scoop\shims
├──  scoop
├──  scoop.cmd
├──  scoop.ps1
```

<BR>
`*ppb -k scoop`は`cmd.exe`が起動しますが他のパスを指定するとどうなるか試してみましょう。  

- `*ppb -k scoop.cmd` PPb上にscoopコマンドの実行結果が出力される
- `*ppb -k scoop.ps1` メモ帳が開きscoop.ps1の内容が出力される

どうやら、`name.cmd`はPPbで直接実行されるようです。  
`scoop.cmd status`と記述することでPPb上に出力されることがわかりました。やったね！  

##### でもやっぱり scoop status を実行したい

`scoop status`をPPb上で実行するには、`path\to\Scoop\shims\scoop`が邪魔になります。  
`scoop`のエイリアスに`path\to\Scoop\shims\scoop.cmd`を設定してみてはどうか？  
Scoop自身が環境変数`SCOOP`を設定しているために問題がでます。  
結論としては、`path\to\Scoop\shims\scoop`を`path\to\Scoop\shims\scoop.backup`などに  
改名することで`scoop status`をPPb上で直接実行できます。  
ただし、`path\to\Scoop\shims\scoop`がどのような使われ方をしているのかわかりませんし、  
Scoopのアップデート時には元に戻ると思います。  

> path\to\Scoop\shims\scoopの中身はsh/bash用のシェルスクリプトになっています  

というわけで直接実行は諦めて`cmd /K scoop status`、`powershell -Command scoop status`  
とするか、PPb以外のシェル上で実行するなどの方法を取りましょう。  
どうしてもPPbで実行したいときのために**`scoop.cmd`**を憶えておくとよいと思います。
