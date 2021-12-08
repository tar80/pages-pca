---
layout: post
title: "*setentryimageを使い易くしたい"
version: PPx181以降
date: 2021-12-08
comment: コマンドの調整。sedの使用をやめた。
categories: PPc
---
### 説明
PPcには任意のファイルに好みのサムネイルを付けられる`*setentryimage`という内蔵コマンドがありますが、<BR>
手順が面倒なので簡略化します。

> ※コマンド内でxargsを使用しています。

### 使い方
- サムネイルを設定したいファイルにカーソルを合わせ、コマンド実行。マークは無視される。
- PPcの独立窓が起動するのでサムネイル用の画像を選択する。

#### 設定
コンテキストメニュー
> 【】内は設定したい表示形式(MC_celS)に変更する。

```
サムネイル設定 = %On *ppb -c %0ppcw.exe -single -bootid:t -choose:con8,%%*regexp(%%FD\%%R,/\\/\\\\/g) | xargs -I {} %0ppcw.exe -r -k *viewstyle -temp 【"サムネイル1(&T)"】 %%: *setentryimage {} -save %%: *zoom +0
;PPb非表示
サムネイル設定 = %Obd %0ppcw.exe -single -bootid:t -choose:con8,%%*regexp(%%FD\%%R,/\\/\\\\/g) | xargs -I {} %0ppcw.exe -r -k *viewstyle -temp 【"サムネイル1(&T)"】 %%: *setentryimage {} -save %%: *zoom +0

サムネイル削除 = *if 9==%*js(PPx.Result = PPx.DirectoryType;) %: *delete "%C" %: *stop
                 *delete "%C:thumbnail.jpg"
                 *zoom +0
 ```
