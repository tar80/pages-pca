---
layout: post
title: PPxとNeovimの連携
version: PPx193以降
date: 2023-12-03
comment: 改稿。*nvimコマンドとlaunchNeovim.jsを更新。
repository: tar80/ppm
categories: PPc Script
---

※最初に、この記事は Neovim の使用を勧めるものではありません。

### 説明

ターミナルエミュレータ + Neovim との連携は困難なので一例として紹介します。
今回は Neovim の側として WindowsTerminal(WT) で説明しています。試していませんが
Alacritty や WezTerm でも流用できると思います。

#### PPx とターミナル上の Neovim の連携が困難である理由

PPx から Neovim にパスを送るにはクライアントサーバとして起動した Neovim にパスを送ります。  
これ自体はしくみを理解すれば難しいことではなく、あらかじめ Neovim を`--listen`オプション付きで起動して、  
PPx から`--server`を指定して`--remote-send`オプションでパスを渡せばよいです。  
<BR>
難しいのは Neovim が未起動の場合で、実際に PPx から WT 上の Neovim にパスを送るには次のような手順を要します。

1. PPx から WT で Neovim を起動するコマンドを実行する
1. WT 上の Neovim の起動を待って、PPx から Neovim にパスを送る

ここでまず問題になるのが 1。  
初回起動時なら WT にコマンドを送ればよいですが、すでに起動している場合は WT にコマンドを送ると
新しい Neovim が開いてしまいます。WT は起動していて Neovim は未起動の場合もあるので、
単純に WT の起動状態によってコマンドを分岐するのは良い方法とは言えません。
<BR>
次に 2。  
PPx が Neovim の起動を検知する簡単な方法がありません。  
常に Neovim を起動していても開き直すことはあるでしょうから、ここを解決しないと手軽に連携できません。

#### 解決法

seeProcess.jsを利用します。このスクリプトを使えば Neovim の起動状態を検知して  
コマンドを実行できるので 1・2 ともに解決します。  
Neovimの起動にはlaunchNeovim.jsを利用します。起動状態、単一エントリと複数エントリ、  
vimdiff などのオプションを調整してNeovimを起動します。  

### 使い方

`seeProcess.js,1,2,3,4` 戻り値:{number} スクリプト実行時のプロセス起動状態 `0`false \| `-1`true

1. {string} 検知するプロセス名
2. {number} 起動待機する時間(ミリ秒)
3. {string} プロセスが未起動の場合に実行するコマンド
4. {number} `3.`のコマンドを実行する際の GUI の大きさ(WscriptSell.run メソッドの引数) `1`通常 \| `2`最小化 \| `3`最大化

<BR>

`launchNeovim.js,1,2,3,4`
戻り値:{number} エラーがあれば`-1`を、なければ`0`を返す

1. {number} プロセスの起動状態 `0`false \| `-1`true
2. {number} クライアントサーバのポート番号(名前付きパイプ)
3. {string} Neovim に送るオプションの種類 `edit` \| `args` \| `diff` \| `command`
   - edit: 単一エントリ
   - args: 複数エントリ(単一エントリを渡すと、内部でeditに変更される)
   - diff: vimdiff
   - command: `4.`で指定したコマンドを実行
4. {string} NeovimのExコマンドを直接指定  
    `3.`がコマンド以外のときは引数としてパスを渡せる。パス区切りは`;`を挟む  
    また、空白を含むパスであっても`"`で括ってはならない

> vimdiff はデフォルトではマークファイルを対象にしますが、4.に`;`区切りのパスが  
> 指定されていれば、そちらを対象にします。パス区切りに空白を使うこともできますが、  
> 空白を含むパスを正しく取得できない可能性があります。

### 設定

1. Neovim のクライアントサーバを設定  
   以下のどちらかの方法があります(`XXX` は任意の待受ポート番号)

   - WT のプロファイルのコマンドラインで指定 `nvim.exe --listen "\\.\pipe\nvim.【XXX】.0"`
   - init.lua に以下を追加

   ```lua
   local pipe = [[\\.\pipe\nvim.【XXX】.0]]
   local server = vim.v.servername

   if server then
       if server ~= pipe then
           local ok = pcall(vim.fn.serverstart, pipe)
           if ok then
               pcall(vim.fn.serverstop, server)
           end
       end
   else
       pcall(vim.fn.serverstart, pipe)
   end
   ```

2. PPx の実行コマンド
   ```text
   *string o,proc=%*script("path\to\seeProcess.js",nvim.exe,9000,"wt -w 1 -p 【Neovimのプロファイル名】",3)
   *string o,error=%*script("path\to\launchNeovim.js",%so'proc',【XXX】,【オプション種類】, "【,exコマンド or 対象パス】"
   *if 0==0%so'error'%:*focus #%*findwindowclass(cascadia_hosting_window_class)
   ```

以前はppmの導入が必要でしたがスクリプトだけで動くように調整しました。  
JScript,CV8 どのライブラリでも動作すると思いますが、ファイル内で日本語を使っているため  
BOM付きUTF-8で保存しないと使えません。  

<script src="https://gist.github.com/tar80/c4542a656e9733271bffcba6bb5e7dac.js"></script>
