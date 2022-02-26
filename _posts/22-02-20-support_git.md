---
layout: post
title: nyagosとpecoを使ったgitコマンドのサポート
version: PPx183以降
date: 2022-02-26
comment: ユーザコマンド\*gitstring内でgitRootを取得するように変更
categories: PPc
---

### 説明

[nyagos](https://github.com/nyaosorg/nyagos/releases)と
[peco](https://github.com/peco/peco/releases)を使ってgitコマンドの実行対象を選択できるようにします。  
gitリポジトリ内で使用でき、以下のような機能があります。

- `git checkout`、`git stash apply`の対象をpecoで選択。
- `git branch`、`git log`、`git stash list`で得たブランチ名、コミットハッシュ、スタッシュ番号を一行編集へ出力。

**PPbを使わずnyagosを使うメリット**

- 窓位置・サイズ調整が容易。
- lua scriptが使える。
  - バッチファイルのようなものですが標準入出力をスクリプト内で扱えます。

### 使い方

![sample]({{ site.baseurl }}{% link /public/img/support_git.gif %})

<BR>
**checkout branch**

コマンドを実行し、ブランチ名を選択して変更。

- git\_checkout.luaは引数が必須。第一引数に`%0%\`、第二引数に`%n`を指定。
- 自前でrepoStat\.jsを実行しません。
- %si"gitBranch"にブランチ名が設定される。


**stash apply**

コマンドを実行し、スタッシュを選択して適用。

- git\_stash\.luaは第一引数にオプションを指定できる。`git_stash.lua "--index"`
- 自前でrepoStat\.jsを実行しません。

**insert branch name**

一行編集上でコマンドを実行し、ブランチ名を選択して挿入。

- git\_string\.luaは引数が必須。第一引数は`%0%\`、第二引数は`branch`

**insert commit hash**

一行編集上でコマンドを実行し、コミットを選択して挿入。

- git\_string\.luaは引数が必須。第一引数は`%0%\`、第二引数は`commit`

**insert stash number**

一行編集上でコマンドを実行し、スタッシュを選択して挿入。

- git\_string\.luaは引数が必須。第一引数は`%0%\`、第二引数は`stash`

> - nyagos、pecoはパスを通しておく必要あり。
> - コマンド内で、PPxWindowModuleを使用。
> - コマンド内で、git,sed,pecoを使用。
> - リポジトリの判別に[repoStat.js]({{ site.baseurl }}{% post_url 22-02-20-script_repoStat %})を使用。  
> - git\_string\.luaでは、出力を一時的に`%si'git_string'`に格納しています。

#### 設定

```text
; ユーザーコマンド
; path\to\の部分は環境に合わせて変更してください。
; posで位置を、modeでサイズを調整します。
; ※ppbw -c nyagos -c の部分はPPbのコンソールプロファイルを利用するための記述です。
;   nyagos -c でも構いません
_Command  = {
gitstring = *ifmatch 0,0%*extract(C"%%si'repoRoot'") %: *script %'scr'%\module\repoStat.js,1
            %Osq *run -noppb -d:%*extract(C"%%si'repoRoot'") -pos:%*windowrect(,l),%*windowrect(,b) ppbw.exe -bootid:n -c nyagos -c mode 75,15&lua_f "path\to\git_string.lua" "%0%\" %*arg(1)
}

; メニュー登録
checkout branch     = %Oq *run -noppb -d:%*extract(C"%%si'repoRoot'") -pos:360,200 ppbw.exe -c nyagos -c mode 60,20&lua_f "path\to\git_checkout.lua" %0%\

stash apply         = %Oq *run -noppb -d:%*extract(C"%%si'repoRoot'") -pos:360,200 ppbw -c nyagos -c mode 60,20&lua_f "path\to\git_stash.lua"
stash apply --index = %Oq *run -noppb -d:%*extract(C"%%si'repoRoot'") -pos:360,200 ppbw -c nyagos -c mode 60,20&lua_f "path\to\git_stash.lua" "--index"

insert branch name  = *gitstring branch
                      *insert %*extract(C"%%si'git_string'")
                      *execute C,*string i,git_string=
insert commit hash  = *gitstring commit
                      *insert %*extract(C"%%si'git_string'")
                      *execute C,*string i,git_string=
insert stash number = *gitstring stash
                      *insert %*extract(C"%%si'git_string'")
                      *execute C,*string i,git_string=
```

<BR>
<script src="https://gist.github.com/tar80/99f0b8a47d928522ed26d35b16cf33e4.js"></script>
