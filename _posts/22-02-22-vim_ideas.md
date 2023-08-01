---
layout: post
title: PPxとvimの連携
version: PPx183以降
date: 2023-08-01
comment: vim-quickrunの項目の誤りを修正
categories: PPc
---

### 説明

PPxとvimの連携についてのアイデア集。  

- PPx -> vim
  - PPxからvimへパスを送る
  - PPxで選択したパスに対するgrep結果をquickfixで開く
  - PPxで選択した2ファイル間の差分を開く

- vim -> PPx
  - PPx設定ファイル(PPxcfg)をハイライト表示
  - 編集中のPPx設定ファイルをPPxに適用
  - 編集中のスクリプトをPPxで実行
  - 編集中のスクリプトをQuickRunで実行

前提として、PPcの`KC_main:Firstevent`に`*set PPX_DIR=%*name(DC,'%0')`を追加しておきます。  
これは起動中のPPxのインストールディレクトリを一時的に環境変数へ追加するための設定で、
複数のPPxを使い分けている場合に効果的です。当然ですがパスを取得できるのはPPxの起動中のみとなります。  

また、vim側に`+clientserver`がサポートされているとファイラとの連携が容易なため、
公式配布バイナリでこれに該当するgvimを使用するものとします。  

#### PPxからvimへパスを送る

パスをgvimに送るだけであれば`path\to\gvim.exe %R`で可能です。  
しかし、これでは毎回gvimのパスを入力する必要があるので、連携を取り易くするため
エイリアスにパスを追加します。

```text
A_exec  = {
gvim = path\to\gvim.exe
}
```

これで`gvim %R`、または`%g'gvim' %R`でパスを送れるようになりました。  
最初からパスが通っていれば、この設定は不要です。  

続けて、必須ではありませんがエディタ用の拡張子判別を作ります。  
以下のようなメニューを作っておけば、ファイルの拡張子に応じてエディタへ送るパスが振り分けられます。  
`%ME_editor`するとマーク、カーソル下エントリに対する拡張子判別が実行されます。

```text
E_editor  = {
:7Z     , *linemessage !"編集できません
:CAB    , *linemessage !"編集できません
:LZH    , *linemessage !"編集できません
:PKZIP  , *linemessage !"編集できません
:RAR    , *linemessage !"編集できません
:JPEG   , *linemessage !"編集できません
:PNG    , *linemessage !"編集できません
:BMP    , *linemessage !"編集できません
:DIR    , %Ox gvim -c "cd %1%\%R" -c "CtrlP"
EXE     , *linemessage !"編集できません
TXT     , *ppe %R
*       , %Ox gvim --remote-tab-silent %*RESULT(LDC)
}
```

#### PPxで選択したパスに対するgrep結果をquickfixで開く

[cmdGrep.js]({{ site.baseurl }}{% post_url 21-03-07-command_Grep %})を使います。  
最初にPPc上で検索対象となるパスを選択し、スクリプトを実行するとgrep検索結果がvimの
quickfixで開かれます。タブで開く処理が面倒だったので常に新規窓で開くようになっています。
また、スクリプト内で窓位置とサイズ調整をしています。

![sample]({{ site.baseurl }}{% link /public/img/vim_grep_qf.gif %})

#### PPxで選択した2ファイル間の差分を開く

vimdiffを使いますが、標準では差分が見難いので表示を調整します。  

- 差分の配色  
お気に入りのcolorschemeでもdiffの表示が見易いとは限りません。それならばdiff部分の配色を
自分好みに変えてしまいましょう。ハイライトの自作なんて大変そうと思うかも知れませんが、
vimがdiffに使うハイライトは4つだけです。colorschemeを自作するよりは簡単です。  

  ![sample]({{ site.baseurl }}{% link /public/img/vim_diff_highlight.jpg %})

  画像のハイライト設定を置いておくので自分好みに調整してみてください。  
  できあがった設定をvimrc内のcolorscheme設定の後に記述すればハイライトを上書きできます。

  ```vim
  hi DiffAdd guifg=#81b5c3 ctermfg=109 guibg=#092532 ctermbg=235 gui=NONE cterm=NONE
  hi DiffChange guifg=#6c806f ctermfg=243 guibg=#20331d ctermbg=234 gui=NONE cterm=NONE
  hi DiffDelete guifg=#2e1b19 ctermfg=234 guibg=#2e1b19 ctermbg=234 gui=NONE cterm=NONE
  hi DiffText guifg=#9ece6e ctermfg=149 guibg=#2d4d19 ctermbg=236 gui=NONE cterm=NONE
  ```

- 長文の折り返し  
  vimdiffはnowrapしていると長文の差分表示までのカーソル移動が面倒で、wrapしていると
  差分がずれて判り難いという厄介なところがありますが、これにはwrap状態のトグルコマンドを作って対応します。  
  単純な関数なので二分割でないと上手く動きませんが、差分を見るだけなら十分使えると思います。

  ```vim
  function! s:SetWrap() abort
    let l:wrap_state = &wrap ? 'nowrap' : 'wrap'
    if &diff
      execute 'wincmd p | setlocal' l:wrap_state '| wincmd p | setlocal' l:wrap_state
    else
      execute 'setlocal' l:wrap_state
    endif
  endfunction

  noremap <silent> <F12> <Cmd>call <SID>SetWrap()<CR>
  ```

vim側の設定ができたらPPxの設定をして差分をvimに送れるようにしましょう。

```text
&Diff = %ORx *string o,path=%FDCN %: *string o,pairpath=%~FDCN
        *if 2==%*js(PPx.Result = PPx.EntryMarkCount;) %: *string o,pairpath=%*regexp("%#;FDCN","s/.+;(.+)/$1/")
        *if 0%*findwindowclass("vim") %: %Obd gvim --remote-send "<cmd>tabe %so'pairpath' | vert diffsplit %so'path'<CR>" >nul %: *stop
        gvim -d "%so'path'" "%so'pairpath'"
```

1行目、マークがあれば最初のエントリ、なければカーソル下エントリ、反対窓を差分対象に  
2行目、マークが二つなら対象を自窓マークエントリに上書き  
3行目、起動済みのvimがあればタブで開く  
4行目、起動済みのvimがなければ新規vimで開く

#### PPx設定ファイルを編集

PPxの設定はPPcustを使えば直接編集できますがvimで編集できると便利です。  
vimでPPX.CFGを開くと、filetypeは`cfg`になっているはずです。ですが、PPxの設定ファイルは
独自の記述方法が使われるため、cfgのsyntaxでは綺麗にハイライトされない箇所があります。  
拙作ですが、[vim-PPxcfg](https://github.com/tar80/vim-PPxcfg)を使ってみてください。
filetypeがPPxcfgに設定されハイライトが適用されます。標準のcfg用ハイライトよりは見易くなると思います。  

#### PPx設定ファイルの編集結果をPPxに反映させる

vimで設定ファイルを編集できてもPPx側で読み込まなければ変更は適用されません。非同期で`PPcust CA`して変更を適用できるようにしましょう。  
filetype=PPxcfgでのみコマンドが有効になるようにしてあるので、vim-PPxcfgを導入しない場合はコマンドを書き換えてください。  

```vim
function! s:LoadPPxcfg() abort
  if &filetype !=# 'PPxcfg'
    echo 'filetype is not PPxcfg'
    return ''
  endif

  call job_start($PPX_DIR . '\ppcustw.exe CA ' . expand('%:p'))
  echo 'PPcust CA ' . expand("%:t")
endfunction


if has('gui_running')
  nnoremap <expr> <C-F9> <SID>LoadPPxcfg()
endif
```

#### 編集中のスクリプトをPPxで実行

スクリプトを書いているとテスト試行したくなることがあります。  
コマンドラインから下のコードを実行すると編集中のファイルをpptray経由で実行できます。  

```vim
execute "!start" $PPX_DIR."\\pptrayw.exe -c *script %"
```

このコマンドは非表示で実行されるため、エラーがわからなかったり無限ループがバックグラウンドで
実行され続ける可能性があります。  
そういったトラブルを避けるにはpptrayの部分をppbに書き換えて実行してください。

#### 編集中のスクリプトをQuickRunで実行

いまさらですがvim-quickrunを使い始めたのでPPxScriptの設定をしてみました。PPXTEXT.DLLを使用します。  
`command`にはppbのフルパスを指定するか、環境変数`PPX_DIR`が設定してあれば`exec`に`${PPX_DIR}/%c`の
ように書けます。

> 設定に誤りがあったので修正しました。  
> typeを`ppx`にして`javascript = {type = 'ppx'}`を設定すると無限ループになってしまうので、
>
> - typeを`javascript`として標準の設定にしてしまう
> - typeを`ppx`として、`javascript = {type = 'ppx'}`は設定せずに`QuickRun ppx` として実行する
>
> の二通りの記述を置いておきます。  
> typeを`javascript`にする場合は、設定内の`type`を削除します。  
> typeを`ppx`にする場合は、`type`に`javascript`を設定しておかないとppxがスクリプトの種類を  
> 認識できずVBScriptとして実行されてしまいます。これはファイル頭にシバン`//!*script`があれば  
> 防げますが、範囲選択をするときには防げません。

<BR>
vimscript
```vim
" let g:quickrun_config.javascript = {
let g:quickrun_config.ppx = {
      \ 'type': 'javascript',
      \ 'command': 'ppbw',
      \ 'cmdopt': '-c *stdout',
      \ 'exec': ['%c %o %%*script(%S)'],
      \ }
```

<BR>
lua
```lua
-- vim.g.quickrun_config.javascript = {
vim.g.quickrun_config.ppx = {
    type = 'javascript',
    command = 'ppbw',
    cmdopt = '-c *stdout',
    exec = { '%c %o %%*script(%S)' }
}
```

<BR>
ついでに、私の環境ではNeovimで選択範囲の実行に`:QuickRun -mode v`
を使うとエラーが出ることがあったのでこのようなキーを設定しています。

```lua
vim.keymap.set({ 'n', 'v' }, 'mq', function()
  local prefix = ''
  local mode = vim.api.nvim_get_mode().mode

  if mode:find('^[vV\x16]') then
    local s = vim.fn.line('v')
    local e = vim.api.nvim_win_get_cursor(0)[1]

    if (s > e) then
      s, e = e, s
    end

    prefix = string.format('%s,%s', s, e)
  end

  vim.cmd(string.format('%sQuickRun', prefix))
end, {})
```

<BR>
試行の度にあっちこっち移動しなくていいのでとても快適。

![sample]({{ site.baseurl }}{% link /public/img/vim_quickrun.webp %})
