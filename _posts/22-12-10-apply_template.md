---
layout: post
title: 新規ファイルにテンプレートを適用
version: PPx188以降
date: 2022-12-10
comment: 投稿。
repository: tar80/ppm-edit
categories: PPc @plugin
---

### 説明

新規ファイルに指定したテンプレートを挿入し、エディタで開きます。  

### 使い方

***テンプレート作成***

1. ファイルタイプごとのテンプレートを作成(`Ctrl+Shift+E`)します。
2. テンプレートにはフォーマットが設けてあるので、それに従ってファイル名を付けます。  
  ![sample]({{ site.baseurl }}{% link /public/img/apply_template1.png %})  
  `名前.適用するファイルタイプの拡張子`

3. S_ppm#user:editorでファイルが開くのでテンプレートを編集します。その際、`;#=`と記入した行  
  は新規作成時の開始行になります。`=`以降にはコメント(テンプレートの説明)を記入します。  
  ![sample]({{ site.baseurl }}{% link /public/img/apply_template2.png %})  

***テンプレート適用***

1. 新規ファイルを作成(`Ctrl+Shift+N`)します。補完候補にテンプレートが並んでいますが、  
  それは置いておいてファイル名を入力します。
2. ファイル名は拡張子を省いた名前だけ入力します。その状態でスペースを入力すると  
  補完候補がリストアップされるので目的の候補を選択して決定します。  
  ![sample]({{ site.baseurl }}{% link /public/img/apply_template3.png %})

3. テンプレート適用済みの新規ファイルがS_ppm#user:editorで開きます。  
  S_ppm#user:editlineが設定されていれば`;#=コメント`の行から開始します。  
  ![sample]({{ site.baseurl }}{% link /public/img/apply_template4.png %})
