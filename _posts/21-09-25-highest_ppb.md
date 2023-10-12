---
layout: post
title: 管理者権限PPb
version: PPx181以降
date: 2023-10-12
comment: 改稿。
categories: PPb
---

## ※※警告※※

**このカスタマイズはお使いのPCのセキュリティに影響を与えます。<BR>
リスクを考慮したうえで利用を判断してください。**

### 説明

Windowsのタスクスケジューラに管理者権限のPPbを登録し、PPb経由でコマンドを実行するための設定。

> PPbには-runasというオプションが用意されていますが、
> それ自体に認証を回避する機能はありません。

### 準備

1. `*ppb -runas`を実行して管理者権限のPPbを起動。認証ダイアログが出るので許可する。
2. 次のコマンドを実行してタスクを登録。  
   `schtasks /create /tn 【タスク名】 /tr "%0%\ppbw.exe $(Arg0)" /sc once /st 00:00 /rl highest /f`  
   `成功: ～`というログが出ればタスク登録完了。
   - 【タスク名】にはタスク登録名を入れる。後で呼び出すときに必要になります。
   - `$(Arg0)`は外部から与えられる引数を示す。そのまま打ち込みます。  
     :o: $(Arg0)  
     :x: $(arg0)
   - `/sc once /st 00:00`は必須項目で外せません。気になるならGUIのタスクスケジューラで
     トリガータブ内の項目を削除。
3. 以下のスクリプトをファイル名exsample\.vbsとしてPPxインストールディレクトリに作成。
   - スクリプトの名称と作成するパスは各自変更してください。

> この方法で起動するPPbはPPcの子プロセスではないため、PPcに設定された環境変数
> などが反映されないことに注意してください。

exsample\.vbs
```vb
Set ss = CreateObject("Schedule.Service")
ss.Connect
Set objTasks = ss.GetFolder("\")
Set objSu = objTasks.GetTask("【タスク名】")
objSu.Run WScript.Arguments(0) & WScript.Arguments(1)
```

### 使い方

- 管理者権限でコマンド実行したい時は、  
  `exsample.vbs -c "*run 【-d:コマンドの親ディレクトリ】 【コマンド】"`
  - vbsの第一引数はPPbのオプション`-k`または`-c`、第二引数はスペースを含む場合`"`で括る。
  - パスが通っていないコマンドは【-d:コマンドの親ディレクトリパス】を指定する。
- 管理者権限のPPbを呼びたい時は、  
  `exsample.vbs -c "*ppc -r -k *ppb"`のように、PPcの子プロセスとして起動すると
  使い易いでしょう。

以上の手順で、認証ダイアログを出さずにコマンド実行できます。  
タスクを削除したくなったら、`schtasks /delete /tn 【タスク名】`でOK。  
難点はタスク実行時に一瞬PPbがちらつくことです。回避する手段の一つとして、  
以下のVBScriptを別タスクに登録する方法があります。  
<BR>

silentTask\.vbs
```vb
Set sh = CreateObject("Wscript.Shell")
sh.run "cmd /c " & WScript.Arguments.Item(0), vbhide
```
<BR>

登録コマンド: `schtasks /create /tn 【タスク名】 /tr "【path\to\silentTask.vbs】 $(Arg0)" /sc once /st 00:00 /rl highest /f`  
実行方法: `path\to\silentTask.vbs 【コマンド】`  

> このタスクで起動したプロンプトは完全に非表示で実行されるため、終了に失敗して
> タスクが残った場合などに判別する方法がありません。  

---

この記事の作成に当たって、[Nodachisoft](https://nodachisoft.com/common/jp/article/jp000107/)、[Windows Script Programming](http://scripting.cocolog-nifty.com/blog/2010/01/post-9f96.html)を参考にさせて頂きました。
