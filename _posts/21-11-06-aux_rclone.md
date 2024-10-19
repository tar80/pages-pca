---
layout: post
title: 'AUX:Rclone'
version: PPx199以降
date: 2024-10-19
comment: ppmの新仕様に対応
repository: 'tar80/ppm-rclone'
categories: PPc @AUX @plugin
---

### 説明

aux:パス機能を用いてRcloneと連携するための設定です。  
公式[ppxaux08](http://toro.d.dooo.jp/slppx.html#ppxaux)をそのまま導入しても
使えますが、ここではより便利に使うための設定を模索しています。

<BR>
<details><summary>Rcloneについて</summary><div>
  <ul>
    <li>Rcloneはオンラインストレージを操作するためのコマンドラインツールです。  
    有名どころのサービスは概ね対応していて、onedriveもgdriveもdropboxもamazon_S3も
    専用のアプリケーションは必要とせず、マウントする必要も無く、同様の操作で扱えます。
    </li>
    <li>通常%userprofile%\.config\rcloneに作られる設定ファイルはトークンなどが
    そのまま書き出されますが、パスワードを設定して暗号化することもできます。
    </li>
  </ul>
</div></details>
<BR>

ppm-rcloneは、aux: sample R8(ppxaux08)と比較すると以下のような特徴があります。

- 非同期実行
- キャッシュ表示による不要な読み込みの抑止
- オンラインストレージのルートより上層にドライブリストを展開
- 更新日時の変更
- `rclone config password`に対応
- 5つまでのファイル操作を同時実行し、30までのファイル操作を保留(stackPPb)

> - 基本非同期実行ですが、PPvでのファイル読み込み時にPPbとの同期通信が発生するため
>   PPvがアクティブ(フォーカス移動、LOASCUSTなど)になったときはキーフックされます
> - カスタマイズにはrclone\.exe、ls2lf\.exe(ppxaux06以降)、PPxKeyModuleが必要です

### 使い方

1. ppm-rclone をインストールしプラグイン設定の編集後にPPcに登録する
2. ドライブルートから、またはパスを直接指定してaux:パスへ移動する  
   `*jumppath aux://S_auxRCLONE/Drives/[<drive name>:[path]]`

- リストの読み込みは設定で指定した有効時間内はキャッシュを読み込みます
  - `F5`でキャッシュを更新できます
  - `PAUSE`キーでrcloneの実行を中断できます
- メインPPb1枚とファイル操作(stackPPb)用に5枚(ID J-N)のPPbを使用します
  - メインPPbは非表示にできませんが、最小化実行は可能です
  - ファイル操作用PPbは積み上げ起点と横幅を指定できます。最小化○ 非表示×
  - 同時実行(stackPPb)を使わない通常のファイル操作も可能です
- マウス左ダブルクリックでのディレクトリ移動は`%k"ENTER"`を実行します  
  `%K"ENTER"`では移動できません

#### メニュー

![sample]({{ site.baseurl }}{% link /public/img/rclone_menu.png %})

| コマンド名        | 内容                                     |
| :---------------- | :--------------------------------------- |
| Rclone            | Rcloneコマンド補完一行編集               |
| Focus PPb         | PPbをフォーカス                          |
| Align PPb         | PPbを整列                                |
| Edit config       | `rclone config edit` を実行              |
| Encryption set    | `rclone config encryption set` を実行    |
| Encryption remove | `rclone config encryption remove` を実行 |

#### キーバインド

PPc

| KEY   | COMMAND                                  |
| :---- | :--------------------------------------- |
| F5    | リスト更新                               |
| PAUSE | 実行中のrcloneコマンドを中断             |
| R     | リネーム                                 |
| C     | コピー                                   |
| M     | 移動                                     |
| D     | 削除                                     |
| K     | ディレクトリ作成                         |
| A     | タイムスタンプ(ファイル書き込み時刻)変更 |
| V     | エントリをPPvの標準入力へ送る            |

> - `A`はDropboxでエラーになりました。ストレージによって使えない場合があるようです。
> - `V`キーはファイルサイズの小さい文書用です。一応、画像も開けます。

<BR>
一行編集

| KEY    | COMMAND                        |
| :----- | :----------------------------- |
| CTRL+1 | 親ディレクトリパスを挿入       |
| CTRL+2 | 反対窓親ディレクトリパスを挿入 |
| CTRL+O | コマンド補完リストをPPeで開く  |

#### 動作例

[stackPPbを使った複数ファイルの移動]({{ site.baseurl }}{% link /public/img/rclone_demo1.webp %})

[設定ファイルの暗号化、暗号化解除]({{ site.baseurl }}{% link /public/img/rclone_demo2.webp %})

リストの非同期読み込み中はスピナーを表示できます。  
左から`dot`, `star`, `toggle`, `triangle`, `block`, `line`  
![spinner]({{ site.baseurl }}{% link /public/img/spinner.webp %})

### Tips

rcloneは、ストレージへのアクセストークン認証サーバ初期値に共用サーバを設定します。
共用サーバは利用者が多い場合に帯域制限がかかりますので、利用者が多いであろう
gdriveはもろに制限を食らいます。そういった理由からgdriveへのアクセスにはとても
時間がかかるのですが、自分用の認証サーバを作るとアクセス速度が向上します。
個人用の認証サーバは有料サービスになっている場合もありますが、gdriveは無料で作れます。  
ちょっと専門的な作業が必要になるので馴染みがないと難しいですが、
やるだけの効果を見込めるためrcloneでgdriveを利用したい方は`rclone client_id`
などで検索して挑戦してみてください。
