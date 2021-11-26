---
layout: post
title: 状況に応じたファイル移動・コピー
version: PPx181以降
date: 2021-11-14
comment: "cmdCopy.jsの第二引数に完了後ハイライト番号を指定可能にした。"
categories: PPc
---
### 説明
送り先ディレクトリ属性を考慮してファイルの移動とコピーを自動設定するスクリプト。
- 反対窓があれば引数に応じて詳細or即実行、なければ詳細実行。初期選択パスはそれぞれ反対窓、%'work'。
- 書庫内で実行した場合、7-zip64.dllを使用して選択ファイルを解凍。
- listfileの拡張子は:XLFを想定。別の拡張子を使う場合、スクリプト内の`:XLF`を置換。
- オプション-compcmdを使って処理後のフォーカスを調整できます。
- ファイルコピー完了後に処理元をハイライトできます。
- コピーは複数ファイルのシンボリックリンクに対応。
  - シンボリックリンク作成時のUAC認証ダイアログを回避したい場合は[管理者権限PPb]({{ site.baseurl }}{% post_url 21-09-25-highest_ppb %})を設定してスクリプトの該当箇所を書き換え。
    ```javascript
    PPx.Execute('%Obn *run -d:%0 ppbw.exe -c schtasks /run /tn 【タスク名】');
    PPx.Execute('*wait 1000,2');
    PPx.Execute(`FOR %%i IN (${commands.join(',')}) DO mklink %%~i`);
    ```

### 使い方
- スクリプトを実行。
- ファイル移動は引数なし、又は0=詳細移動、1=即移動。
- ファイルコピーは第一引数0=詳細コピー、1=即コピー、2=シンボリックリンク。第二引数=完了後ハイライト色番号。

#### 設定
```clean
A_exec = {
scr  =  ;スクリプトをまとめておくディレクトリパス
work =  ;作業用のディレクトリパス
}

PPcメニュー
移動(&M)               = *script %'scr'%\cmdMove.js,1
詳細移動(&M)           = *script %'scr'%\cmdMove.js
コピー(&C)             = *script %'scr'%\cmdCopy.js,1,1
詳細コピー(&C)         = *script %'scr'%\cmdCopy.js,0
シンボリックリンク(&L) = *script %'scr'%\cmdCopy.js,2
```
<BR>
<script src="https://gist.github.com/tar80/56a26446a5cc5e52e2d228a84b6fd60f.js"></script>
