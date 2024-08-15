---
layout: post
title: このパス以降に背景画像を設定（最適化）
version: PPx198以降
date: 2024-08-15
comment: 投稿。
repository: 
categories: PPc
---

### 説明

以前の記事 [このパス以降に背景画像を設定]({{ site.baseurl }}{% post_url 21-12-08-set_bg %}) では
特定パスで変更された背景設定を全体設定(`*`)で元に戻す方法を解説しました。
この方法は簡単とはいえずも比較的わかり易い方法であったと思います。
ただし2点ほど問題がありました。  

1. ディレクトリの更新ごとに無駄な背景読み込み処理が走る
1. 背景を設定した特定のパスから別のパスに移動するとき、移動先が個別の
   ディレクトリ設定をもつ場合に背景の更新処理が行われない

これらが気になる人もいるでしょう。今回の記事ではより複雑になりますが、
全体設定(`*`)を使わずLOADEVENTに*linecustを登録する方法を解説します。

#### 設定

1. 最初に、設定されているなら`XC_dset:*`の`cmd:`から背景画像の削除設定を除去する  
   `*deletecust X_bg:P_%n%:*deletecust X_bg:T_%n%:*color back` のような部分です。
1. 次に`XC_dset`の特定のパスに`*diroption`を設定する
   ```text
   *setcust X_bg:P_%n=<背景画像のパス>%:*setcust X_bg:T_%n=20%:*color back%:*oneshot dset,KC_main:LOADEVENT,%FDV,%%FDV,*deletecust X_bg:P_%n%%:*deletecust X_bg:T_%n%%:*color back
   ```
1. `_Command`に*linecustの登録コマンドを設定する
   ```text
   _Command = {
    oneshot = *linecust %*arg(1)%n,%*arg(2),*if ("%n"=="%%n")&&(0!=%%*js("PPx.result='%*arg(4)'.indexOf('%*arg(3)');"))%%:*linecust %*arg(1)%n,%*arg(2),%%:%*arg(5)
    }
   ```
1. 最後にメニュー登録してあればそちらも修正する
   ```text
   ;コンテキストメニュー
   パス限定背景(&B) = %Obd %0ppcw.exe -single -bootid:t -choose:con8,%%*regexp("%%FD\%%R",/\\/\\\\/g) | xargs -I {} %0ppcw.exe -r -k *diroption -thisbranch cmd "%(%(*setcust X_bg:T_%n=20%:*setcust X_bg:P_%n={}%:*color back%:*oneshot dset,KC_main:LOADEVENT,%FDV,%%FDV,*deletecust X_bg:P_%n%%:*deletecust X_bg:T_%n%%:*color back%)%)"%&*jumppath -savelocate -entry:"%R" -refreshcache
   パス背景解除(&Q) = *ifmatch 0,0%*getcust(XC_dset:%FDN\) %:%"パス背景解除"%I"このパスに背景画像は設定されていません"%:*stop
     *diroption -thisbranch cmd ""%:*linecust dset%n,KC_main:LOADEVENT,%:*deletecust X_bg:P_%n%:*deletecust X_bg:T_%n%:*color back
   ```

\*oneshotコマンドの説明  
`*oneshot <ラベル名>,<テーブル:イベント名>,<word1>,<word2>,<実行コマンド>`  

\*oneshot実行時とPPcIDが一致し、\<word1>が\<word2>に含まれなければ、一度だけ
コマンドを実行します。  
例として、\<word1>に`%FD`、\<word2>に`%%FD`を設定した場合、  
\<word1>は*oneshot実行時に展開され、\<word2>はイベント実行時に展開されます。  
必ず発動させたい場合は\<word1>=`0`、\<word2>=`1`のように設定します。  
\<実行コマンド>の`%`はエスケープが必要です。また、`,`が含まれる場合は`"`で
全体を囲む必要があります。  

> - XC_dsetで、特定パス以降に設定を適用したいときはパスの末尾を`\`(バックスラッシュ)にする
> - ppmでXC_dset:listfileに仮想エントリ用設定をしていると、パス以降に直接移動
> したときに背景が変更されません。修正予定です。

<BR>
設定をユーザーコマンドに分けている理由は、\*diroption cmd:内で使用される
`"`(ダブルクオテーション)は`""`にエスケープされるのですが、\*diroption cmd:内
で呼び出される\*linecustに`""`が含まれると、そこでコマンドラインが途切れてしまうからです。
この点は筆者がなにか勘違いしているか、環境が影響している可能性もあります。  
どちらにしろ、イベントに*linecustを設定したいことはあるので使い回せると思います。  
<BR>
ちょっと複雑で設定に梃摺るかもしれませんが、筆者の環境では問題なく動作しています。  
興味があれば試してみてください。  

