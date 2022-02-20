---
layout: post
title: repoStat.js
version: PPx183以降
date: 2022-02-20
comment: 投稿
categories: Script
---

### 説明

git repositoryのルートパスと現在のブランチ名を、`%si'repoRoot%n'`と`%si'gitBranch%n'`に設定するスクリプト。  
%si変数はPPcのIDごとに設定され、ディレクトリ移動時に削除されますが、現在のパスが`aux:S_git-*`を示す限り維持されます。これにはPPcを終了した場合も含まれます。

### 使い方

v"gitBranch"として情報行に表示したり、`*run -d:%*extract(C%%si'repoRoot%n') git log`のように親パスの指定に利用する。  
  
<BR>
<script src="https://gist.github.com/tar80/4132d64460bab0b94339a8da7d2ae82a.js"></script>
