---
layout: post
title: PPvプラグイン
version: PPx191+1以降
date: 2023-06-17
comment: 投稿。
repository: tar80/ppm-view
categories: PPc PPv @plugin
---

### 説明

PPv をより便利に操作するための設定集です。様々な機能を実装しています。

- [カーソル位置に応じて PPv を移動]({{ site.baseurl }}{% post_url 19-11-11-moving_ppv %})
- [PPv の挙動を動的に変更]({{ site.baseurl }}{% post_url 20-05-02-behavior_ppv %})
- [窓数に合わせた連動ビューの制御]({{ site.baseurl }}{% post_url 20-12-29-syncview %})
- [bat を使って PPv で構文ハイライト]({{ site.baseurl }}{% post_url 23-06-16-ppv_syntax %})
- ラップ・最全面・透明度のトグルキー
- ファイル内検索の強化
- 履歴機能とブックマーク
- パスを別 PPv で開く
- ViLikeKeys

### 使い方

- **カーソル位置に応じて PPv を移動**

  記事からの変更点として、起動する PPv ごとに有効/無効を設定できるようになりました。
  そのため`%si'vState'`変数は不要になり、自動位置調整の一時停止には`%sp'restppv'`を使用します。
  また X_vpos=0 のときのみ対象となります。

<BR>
- **PPv の挙動を動的に変更**

    記事から多少変更されて一行編集がどこかへ飛んでいかないようになっています。
    一行編集の起動時に処理を停止する変数`%sp'hmppv'=1`が設定され、終了時に削除されます。

<BR>
- **窓数に合わせた連動ビューの制御**

    PPc の窓数に合わせて、1 枚なら通常起動、2 枚なら`*capturewindow`で反対窓に取り込みます。
    その際`X_win:V`の設定を指定できますが、capturewindow 取り込み時のタイトルバーは強制的に off になります。
    また、取り込み前のサイズとXV_tmod(キャレット状態)は維持されます。

    [連動ビュー動作例]({{ site.baseurl }}{% link /public/img/view_sync.webp %})

    | key | command           |
    | :-: | :---------------- |
    |  Y  | 連動ビュー トグル |

<BR>
- **bat を使って PPv で構文ハイライト**

    bat と nkf を使用します。ハイライト適用時はキャレットモードで正確なカーソル位置を取得できなくなります。

    | key                   | command                  |
    | :-------------------- | :----------------------- |
    | (PPc) N               | PPv 起動                 |
    | (PPv) CTRL+SHIFT+UP   | 前のファイル             |
    | (PPv) CTRL+SHIFT+DOWN | 次のファイル             |
    | (PPv) SHIFT+F5        | 構文ハイライト再読み込み |

<BR>
- **ラップ・最全面・透明度のトグルキー**

    [トグル動作例]({{ site.baseurl }}{% link /public/img/view_toggle.webp %})

    |  key    | command            |
    | :-----: | :----------------- |
    |  F12    | ラップトグル       |
    | SHIFT+T | 最全面トグル       |
    | SHIFT+O | 透明度二段階トグル |

<BR>
- **ファイル内検索の強化**

    カーソル下単語を検索する機能、カーソル下単語を選択した状態で入力バーを起動する機能を提供します。  
    正規表現がいい加減なので単語抽出の精度は低いです。

    | key | command            |
    | :-: | :----------------- |
    |  F  | 後方検索           |
    |  B  | 前方検索           |
    | \*  | カーソル下単語検索 |

<BR>

- **履歴機能とブックマーク**

  [これ]({{ site.baseurl }}{% post_url 21-06-01-quick_bookmark %})の PPv 版です。ユーザーヒストリ(x)を使用します。

  [ブックマーク動作例]({{ site.baseurl }}{% link /public/img/view_bookmark.webp %})

  |   key   | command          |
  | :-----: | :--------------- |
  |    0    | 履歴リスト       |
  | SHIFT+0 | ブックマーク登録 |

<BR>
- **パスを別 PPv で開く**

    カーソル下のパス及び URL を別 PPv で開きます。
    副産物としてスペース区切りの範囲を選択するコマンドが出来ましたが上手く動作しない事があります。
    とくに bat 使用時はカーソル位置が正確に取れないようなので範囲取得できません。

    [パスを開く動作例]({{ site.baseurl }}{% link /public/img/view_openpath.webp %})

    |     key      | command                |
    | :----------: | :--------------------- |
    | CTRL+SHIFT+O | パスを開く             |
    |      S       | スペース区切り範囲選択 |

<BR>
- **ViLikeKeys**

    PPv のキーバインドを Vi と似たような感じにします。

    |   key   | command          |
    | :-----: | :--------------- |
    |    :    | コマンド入力バー |
    |    G    | ファイル頭に移動 |
    | SHIFT+G | ファイル末に移動 |
    | CTRL+G  | 行ジャンプ       |
    | H,J,K,L | カーソル移動     |
    |    N    | 次を検索         |
    | SHIFT+N | 前を検索         |
