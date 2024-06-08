---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## NestJSの基本要素

原則として、以下の3つが揃って一つの機能が出来上がる

- NestJSにおける最もコアとなる要素 
	- Controller
	- Service
	- Module

## NestJSのアーキテクチャ

- ベースはアプリケーション、ルートモジュール
- 機能を追加したい場合はFeatureモジュール、サービス、コントローラを追加して、それをルートモジュールに登録していくのがNestJSの開発の基本となる

![[Pasted image 20240605145607.png]]