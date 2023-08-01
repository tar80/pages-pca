---
layout: post
title: 'AUX:Rclone'
version: PPx191+05以降
date: 2023-07-13
comment: プラグイン化
repository: 'tar80/ppm-rclone'
categories: PPc @AUX @plugin
---

### 説明

aux:パス機能を用いて Rclone と連携するための設定です。  
公式[ppxaux06](http://toro.d.dooo.jp/slppx.html#ppxaux)をそのまま導入しても使えますが、ここではより便利に使うための設定を模索しています。

<BR>
<details><summary>Rcloneについて</summary><div>
  <ul>
    <li>Rcloneはオンラインストレージを操作するためのコマンドラインツールです。  
    有名どころのサービスは概ね対応していて、onedriveもgdriveもdropboxもamazon_S3も
    専用のアプリケーションは必要とせず、マウントする必要も無く、同様の操作で扱えます。
    </li>
    <li>通常%userprofile%\.config\rcloneに作られる設定ファイルはトークンなどがそのまま書き出されますが、
    パスワードを設定して暗号化することもできます。
    </li>
  </ul>
</div></details>
<BR>

ppm-rcloneは、aux: sample R6(ppxaux06)と比較すると以下のような特徴があります。

- オンラインストレージのルートより上層へ移動すると`rclone listremotes`をドライブリストとして開く
- リネーム、更新日時の変更、ストレージ間でのファイル操作に対応
- `rclone config password`に対応。PPc 起動後の最初の aux:パス読み込み時にパスワード入力が必要  
    ※PPxのパスワード認証機能はドライブリストと相性が悪かったため独自入力を使用
- 非同期でリストを読み込むオプション(EcmaScriptのみ)

  > 非同期読み込みでは aux:パスの仕様上、実行時に一度キャッシュを読み込み、
  > 更新後に再度aux:パスを開き直す処理をしています。
  > そのためプラグイン側のバグなどで読み込みが連続的に発生する可能性があります。  
  > また、非同期読み込みはディレクトリの移動時にのみ実行されます。
  > 非同期読み込み中に別のパスに移動しても、処理が更新されることはなく最初のパスの取得を続行します。  
  > 非同期読み込みは個人的な利用のため用意しているので、安定している同期読み込みの利用を推奨します。

> カスタマイズには PPxKeyModule と ls2lf\.exe(ppxaux06)が必要です。

### 使い方

- ppm-rclone をインストールして設定する
- `*jumppath aux://S_auxRCLONE/(Rcloneの設定名):`または`%K"@\L"`などでパスに移動

動作例(非同期読み込み)

![sample]({{ site.baseurl }}{% link /public/img/aux_rclone.webp %})


#### キーバインド

PPc

| KEY | COMMAND                                  |
| :-- | :--------------------------------------- |
| F5  | リスト更新                               |
| R   | リネーム                                 |
| C   | コピー                                   |
| M   | 移動                                     |
| D   | 削除                                     |
| K   | ディレクトリ作成                         |
| A   | タイムスタンプ(ファイル書き込み時刻)変更 |
| V   | エントリを PPv 標準入力で開く            |

> - `A`はDropboxでエラーになりました。ストレージによって使えない場合があるようです。
> - `V`キーはファイルサイズの小さい文書用です。一応、画像も開けます。
