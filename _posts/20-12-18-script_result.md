---
layout: post
title: result.js
version: PPx181以降
date: 2021-11-01
comment: スクリプトを書き直した。
categories: Script
---
### 説明
ユーザーコマンド`%*result()`を使い、変数の参照を一元的にするスクリプト。
- `%*js(PPx.Result=PPx.○○○)`を`%*result(○○○)`ようにして参照できます。
- 自作変数を登録できます。

##### 変数の説明

| variable | value |
|:-|:-|
| filetype  | 内容から拡張子を判別。不明の場合、`---`を返す。 |
| exists    | 存在確認。存在しない場合は`0`を、存在する場合は`1`を返す。<BR>(※第二引数指定可。指定なしなら`%R`) |
| getpath   | １枚窓なら`%'work'`を、反対窓が存在するなら`%2`を返す。 |
| myrepo    | 現在のパスが`%'myrepo'`内であれば`0`を、違えば`-1`を返す。 |
| shapecode | 改行を含む編集中コマンドをPPxにコマンド登録可能な状態に整形して返す。 |
| lfnames | リストファイル内で、マークのあるエントリ情報をスペース区切りでまとめて返す。<BR>(※第二引数指定可。指定なしならPPx.Entry.`Name`) |
| LDC | シンボリックリンクと通常のパスを判別して(リンク先)フルパスを返す。 |

### 使い方
`%*result()`を実行して戻り値を得る。第一引数は変数名、第二引数は自作変数用。%FDCなどの複数回実行されるマクロ文字に対応するために利用できます。
- PPx.Entry.commentを得る場合`%*result(Entry.Comment)`
- listfile上のマークエントリからPPx.Entry.Commentを得る場合`%*result(lfnames,Comment)`


#### 設定
```clean
;エイリアス
A_exec = {
myrepo = ;gitリポジトリ(rootのパス)
scr    = ;スクリプト親ディレクトリパス
work   = ;作業用ディレクトリパス
}

;ユーザーコマンド
_Command = {
RESULT   = %*script(%'scr'%\result.js,%*arg(1),%*arg(2))
}
```
<BR>
<script src="https://gist.github.com/tar80/09b8d7bcf0e0caed3194c310df0805b0.js"></script>
