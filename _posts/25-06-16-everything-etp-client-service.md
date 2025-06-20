---
layout: post
title: Everything-ETPサーバーをサービス登録 
version: PPx203以降
date: 2025-06-16
comment: 投稿。
repository: tar80/ppm-etp
categories: "@plugin"
---

### 説明

最近のEverythingをPPxモジュールから使う場合、ETP経由で通信になると思います。ETPサーバーを利用する場合、管理者権限のEverythingをバックグラウンドで起動しておく必要がありひと手間必要ですが、ETP clientはサービス登録できるので設定方法を説明します。

#### 設定

1. Everythingインデックスサービスを登録している場合は解除します
2. Everything.exe単体を新規ディレクトリにコピーします  
   - ディレクトリ名はなんでもよいですが、Everything-etp-serviceなど
   わかりやすい名前をつけて区別します
3. コピーしたEverithyng.exeを起動し、設定します  
   - コピーしたEverythingはサービスとして起動され、GUIから操作することはないため、ETPとインデックス関連の設定だけしておけばよいです
4. 設定が終わったらEverythingを終了します
5. プロンプトを起動し、`Everything.exe -install-client-service`を実行します

サービスの解除はプロンプトから、`Everything.exe -uninstall-client-service`
