---
layout: post
title: 管理者権限PPb
version: PPx181以降
date: 2021-09-25
comment: 投稿。
categories: PPb
---
## ※※警告※※
**このカスタマイズはお使いのPCのセキュリティに影響を与えます。<BR>
リスクを考慮したうえで利用を判断してください。**

### 説明
Windowsのタスクスケジューラに管理者権限のPPbを登録し、PPb経由でコマンドを実行するための設定。

> PPbには-runasというオプションが用意されていますが、
  それ自体に認証を回避する機能はありません。

### 準備
1. `*ppb -ranas`を実行して管理者権限のPPbを起動。認証ダイアログが出るので許可する。
1. 次のコマンドを実行してタスクを登録。<BR>
  `schtasks /create /tn 【タスク名】 /tr "%0%\ppbw.exe $(Arg0)" /sc once /st 00:00 /rl highest /f`<BR>
  "成功: ～"というログが出ればタスク登録完了。<BR>
 - 【タスク名】にはタスク登録名を入れる。後で呼び出すときに必要になります。
 - `$(Arg0)`は外部から与えられる引数を示す。そのまま打ち込みます。<BR>
    :o: $(Arg0)<BR>
    :x: $(arg0)
 - `/sc once /st 00:00`は必須項目で外せません。気になるならGUIのタスクスケジューラで
   トリガータブ内の項目を削除。
1. 以下のスクリプトをファイル名【exsample】\.vbsとしてPPxインストールディレクトリに作成。<BR>
   - スクリプトの名称と作成するパスは各自変更してください。<BR>

```vb
Set ss = CreateObject("Schedule.Service")
ss.Connect
Set objWd = ss.GetFolder("\")
Set objTask = objWd.GetTask("【タスク名】")
objTask.Run WScript.Arguments(0) + WScript.Arguments(1)

```

### 使い方
- 管理者権限でコマンド実行したい時は、<BR>
  `【exsample】.vbs -c "*run 【-d:コマンドの親ディレクトリ】 【コマンド】"`<BR>
  - \.vbsの第一引数はPPbの`-k`又は`-c`、第二引数はスペースを含む場合`"`で括る。<BR>
  - パスが通っていないコマンドは【-d:コマンドの親ディレクトリパス】を指定する。
- 管理者権限のPPbを呼びたい時は、<BR>
  `【exsample】.vbs -c "*run -d:%0 ppbw.exe"`


以上の手順で、認証ダイアログを出さずにコマンド実行できます。
難点はコマンド実行時に一瞬PPbがちらつくことですが回避方法は思い当たりません。<BR>
タスクを削除したくなったら、`schtasks /delete /tn 【タスク名】`でOK。
<BR><BR>

---

この記事の作成に当たって、[Nodachisoft](https://nodachisoft.com/common/jp/article/jp000107/)、[Windows Script Programming](http://scripting.cocolog-nifty.com/blog/2010/01/post-9f96.html)を参考にさせて頂きました。



