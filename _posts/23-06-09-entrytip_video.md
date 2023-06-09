---
layout: post
title: mpvで動画プレビュー
version: PPx191以降
date: 2023-06-09
comment: 投稿。
repository:
categories: PPc
---

### 説明

PPc では`*entrytip preview`を使用してカーソル下エントリの内容をプレビューできます。
動画のプレビューには標準で`*wmp`コマンドが用意されていますが、一部の mp4 や webm など
wmp(windows media player)では再生できない動画も割とあります。
コーデックをインストールすれば再生は可能でしょうが、元々対応している mpv を使うと
手軽にプレビュー できます。
<BR><BR>

> [mpv](https://mpv.io/)は [mplayer](http://www.mplayerhq.hu/design7/news.html) からフォークされた
> CUI 上で動作するメディアプレイヤーです  
> 軽快・高機能で様々な動画形式にネイティブ対応しています

### 使い方

1. mpv をインストール
1. 設定を適用し、対応する拡張子を持つエントリ上で`*entrytip preview`を実行  
   またはコマンド`*entrytip preview -c %%Obd mpv.exe --wid=%%si'TipWnd' "%%si'TipTarget'"`を直接実行

![sample]({{ site.baseurl }}{% link /public/img/entrytip_video.webp %})

> mp4をwmp、webmをmpvでプレビューしています

#### 設定

mpv のパスが通っていない場合はフルパスを指定してください。  

```config
E_TipView = {
:MP4    ,%Obd mpv.exe --wid=%N-F "%si'TipTarget'"
:EBML   ,%Obd mpv.exe --wid=%N-F --no-audio --video-zoom=1.0 "%si'TipTarget'"
}
```

<BR>
登録する拡張子が多くなる場合は`_command`に登録するといいかもしれません。  

```config
_Command = {
mpv = %Obd mpv.exe "%si'TipTarget'" --wid=%N-F %*arg(1)
}

E_TipView = {
:MP4    ,*mpv
:EBML   ,*mpv "--no-audio --video-zoom=1.0"
}
```

<BR>
> `%si'TipWnd'`と`%N-F`は同値です
