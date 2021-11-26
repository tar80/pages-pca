---
layout: post
title: "*setentryimageを使い易くしたい"
version: PPx181以降
date: 2021-11-06
comment: コマンドに追記した。
categories: PPc
---
### 説明
PPcには任意のファイルに好みのサムネイルを付けられる`*setentryimage`という内蔵コマンドがありますが、<BR>
手順が面倒なので簡略化します。

 > ※コマンド内でsed, xargsを使用しています。

### 使い方
 - サムネイルを設定したいファイルにカーソルを合わせ、コマンド実行。
 - PPcの独立窓が起動するのでサムネイル用の画像を選択する。

#### 設定
コンテキストメニュー
```clean
サムネイルの設定 = *viewstyle -temp "サムネイル1(&T)" ; 設定したい表示形式(MC_celS)
                   %On *ppb -c %0ppcw.exe -single -bootid:t -choose:con | sed -e s/\\/\\\\/g | xargs -I {} %0ppcw.exe -r -k *setentryimage {} -save

; PPb非表示
サムネイルの設定 = *viewstyle -temp "サムネイル1(&T)" ; 設定したい表示形式(MC_celS)
                  %Obd %0ppcw.exe -single -bootid:t -choose:con8 | sed -e s/\\/\\\\/g | xargs -I {} %0ppcw.exe -r -k *setentryimage {} -save
 ```
