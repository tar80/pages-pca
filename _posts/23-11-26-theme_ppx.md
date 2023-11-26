---
layout: post
title: PPxにカラーテーマを適用する
version: PPx194以降
date: 2023-11-26
comment: 投稿。
repository: tar80/ppm-theme
categories: PPc PPv PPb PPe @plugin
---

### 説明

以前に投稿した[PPvにカラーテーマを適用する]({{ site.baseurl }}{% post_url 21-03-29-color_theme %})
では、Webサイトからクリップボード経由で色情報を取得していましたが  
[mbadolato/iTerm2-Color-Schemes](https://github.com/mbadolato/iTerm2-Color-Schemes)
から直接取得することで、より簡単にテーマを変更できるようにしました。  
テーマごとの標準20色(前景、背景、カーソルライン、選択、基本8色、明8色) + 暗8色を追加した28色を利用できます。

![sample]({{ site.baseurl }}{% link /public/img/theme.webp %})

### 使い方

1. 最初に`*ppmEdit`で`ppm-theme`の設定をします。主に配色の指定です。  
   ※テーマによって大きく色が異なるので、一度設定を適用しておいて後から  
   テーマに合わせて配色するのがよいと思います。
1. 次に`*ppmTheme`を実行します。専用のPPcが全画面で起動します。  
   気に入ったテーマにカーソルを合わせ、`ENTER`キーで決定します。
   > 色見本と色設定のファイル名が統一されていないためパスの取得に失敗する場合があります。
1. 背景色の選択を求めるダイアログが表示されます。ここで選択された背景色を基準に  
   独自の暗色が生成されます。

`*ppmThemeColors`を実行すると色見本をPPvで閲覧できます。

> - 標準の色設定は iceberg です。
> - 変更された色設定を完全に適用するには再起動が必要です。
> - 現在適用中のテーマ名は`S_ppm#user:th_name`を参照します。

#### switchmenuへの登録

コマンドを使わずメニューから操作する一例として、スイッチメニューに新規メニュー登録する例です。

1. スイッチメニューを表示し、`0:Create new menu`を選択します。
2. 適当なメニュー名を入力します。今回は"Theme"にします。
3. エディタでTheme.cfgの編集が始まります。あらかじめテンプレートが読み込まれているので  
    `M_ppmSwitchSub`に、テーマ変更用コマンドと色見本表示コマンドを登録します。  
    `*string o,pwd=`の部分はppm-themeのパスを直接入れてもよいです。`%sgu'ppmrepo'\ppm-theme`とか。

   ```clean
    -|M_ppmSwitchSub =
    M_ppmSwitchSub = {
    テーマの選択(&T) = *string o,pwd=%*script(%sgu'ppmlib'\expandSource.js,ppm-theme,path)
     *script %so'pwd'\dist\selectTheme.js
    色見本閲覧(&V) = *string o,pwd=%*script(%sgu'ppmlib'\expandSource.js,ppm-theme,path)
     *setcust @%so'pwd'\colortest\setup.cfg
     %Oq *ppv %so'pwd'\colortest\theme.colortest%&*deletecust CV_hkey:COLORTEST
    }
   ```


4. 編集が終わったら、`3.`で作ったサブメニューをスイッチメニューに登録するため、  
   もう一度スイッチメニュー`0:Create new menu`を選択します。
5. エディタでswichmenu.cfgの編集が始まります。項目`&n:Theme =`が追記されているので  
   必要な箇所を編集します。
6. スイッチメニューを再読み込みするとメニューが反映されます。

##### メニュー項目例

- 気に入ったテーマをエイリアスとして登録  

  ```clean
   M_ppmSwitchSub = {
   ...
   -- =
   &iceberg = *string o,pwd=%*script(%sgu'ppmlib'\expandSource.js,ppm-theme,path)
    *script o,theme=%so'pwd'\themes\windowsterminal\iceberg-dark.json
    *script %so'pwd'\dist\applyTheme.js,%so'theme'
    ; *setcust C_entry=...
    ; *setcust C_eInfo=...
    ; ...
    ; %K"@LOADCUST"
   &tokyonight = *string o,pwd=%*script(%sgu'ppmlib'\expandSource.js,ppm-theme,path)
    *script o,theme=%so'pwd'\themes\windowsterminal\tokyonight.json
    *script %so'pwd'\dist\applyTheme.js,%so'theme'
    ; *setcust C_entry=...
    ; *setcust C_eInfo=...
    ; ...
    ; %K"@LOADCUST"
   }
  ```

- 現在適用中のテーマを表示
  ```clean
  M_ppmSwitchSub = {
  ...
  -- =
  ?b;現在のテーマ: %*getcust(S_ppm#user:th_name) = %K"@null"
  }
  ```
- テーマエイリアスのサブメニュー化
  ```clean
  M_ppmSwitchSub = {
  ...
  -- =
  現在のテーマ: %*getcust(S_ppm#user:th_name) = ?M_ppmSwitchSub1
  }
  M_ppmSwitchSub1 = {
  &iceberg = ...
  &tokyonight = ...
  ...
  }
  ```

#### 補足

`mbadolato/iTerm2-Color-Schemes`はコンソール用の設定なので、PPvやPPbはともかく
PPcのテーマとして使うには色数が物足りないです。大まかな色設定はテーマに任せて
細かい部分は自分で色設定を調整したり、PPcには適用せずPPv,PPb用に設定するなど
工夫してみてください。
