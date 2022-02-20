---
layout: post
title: 初期化後再設定
version: PPx181以降
date: 2021-11-27
comment: 処理中断キーをSPACEからBACKSPACEに変更した。
categories: PPc
---
### ※※警告※※

このカスタマイズを実行すると<span style="color: red;">PPxの設定が全削除されます。</span><BR>
**必ずバックアップを取って下さい。**<BR>
また、おかしな動作になったときに自分で対処する自信がない人は導入を見合わせて下さい。

### 説明
初期化→再設定の自動化。<BR>
設定ファイルを分割管理していると、初期化→再起動→再設定→再起動の流れが面倒なので一連の流れを簡略化。

### 使い方
1. 最初に設定ファイルをマークした状態でリストファイルを作る。<BR>
  ```*makelistfile -basic -marktag -comment xr_reset.xlf```
  - 保存するリストファイル名は"xr_"から始まる名前にする。
2. 1.のリストファイルを%'list'ディレクトリに持ってくる。
3. メニューからリセット(&R)すると、適用するリストファイルを選択できる。<BR>
4. リストファイルを開いてマーク状態を確認後、ESC押下で自動的に初期化→再設定が始まる。
  - BACKSPACEで処理を中断可能。

> - マークしたファイルに対して`PPCUSTW CA`が実行されます。`PPCUSTW CS`の処理が必要であればスクリプトの該当箇所を書き換えて下さい。
> - 開いているPPcで直接コマンドを実行するとエラーが出るので、
  一度PPcを閉じた後に新規PPc(ID=CS)で処理を実行しています。
> - マークしたエントリはリストの上から順番に実行されます。
  マークした順番は無視されますので注意してください。
> - キーの一時変更にKeyModuleを使っています。

※画像は古いカスタム状態です。
![sample]({{ site.baseurl }}{% link /public/img/command_reset.gif %})

### 設定
```clean
;エイリアス
A_exec = {
list =  ;読み書きするリストをまとめておくディレクトリパス
scr  =  ;スクリプト親ディレクトリパス
}

;メニュー
リセット(&R) = *string o,markfile=%*input("%'list'%\xr_" -title:"マークファイルの選択" -select:l -k *completelist)
               *closeppx
               %On- *ppc -single -bootid:S %so"markfile" -k *mapkey use,K_resetMap %%: *color back H110022

K_resetMap = {
ESC   , *script %'scr'%\cmdReset.js
BS , *mapkey delete,K_resetMap %: *color back
}
```
<BR>
<script src="https://gist.github.com/tar80/acab83654c5e5a9dd6486fcc0efd39e7.js"></script>
