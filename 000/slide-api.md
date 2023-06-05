# 勉強会：APIについて

---

## はじめに

- 前半はAPIとは何ぞやという方向け
- 後半はクライアントサーバ分離等、アーキテクチャの話も
- フロントエンドフレームワークや認証認可については話しません

---

### APIとは

- Application Programming Interfaceの略です。
<https://data.wingarc.com/what-is-api-16084>
- アプリケーション同士で連携をする為の、外部との窓口のようなもの
- 今回取り上げるのはWeb APIについて

---

### Web APIとは

- HTTPプロトコルでやりとりするアプリケーション間のインターフェース
  - JSONやXMLなどの構造化されたテキストデータでやりとりする
    - [Web APIの代表例](https://blog.hubspot.jp/api-web#:~:text=Web%20API%E3%81%A8%E3%81%AF%E3%80%81http,%E3%81%AB%E5%85%B1%E6%9C%89%E3%81%99%E3%82%8B%E4%BB%95%E7%B5%84%E3%81%BF%E3%81%A7%E3%81%99%E3%80%82)
    - [connpass API](https://connpass.com/about/api/)
    - [国立国会図書館サーチ-書影API](https://iss.ndl.go.jp/information/api/api-lists/thumbnail_info/)

---

### Web APIの種類

- 社外に公開して幅広く利用してもらうもの
  - Twitter等が開発者向けに提供しているAPI
- 組織や開発チームで使用する非公開のもの
  - Webアプリケーションのバックエンド
  - 各アプリケーションで利用したい共通機能のAPI

---

### Web APIを公開する利点

- 認知度向上によるWebサービスの普及
- 他サービスとの連携によるサービス間の作業の自動化
- サービス開発者以外のリソースを使ってサービスを成長

---

### Web APIを開発で利用する利点

- Webアプリケーションのバックエンドとして利用する場合
  - クライアントとAPIサーバを分離できる
    - 個別に保守しやすい状態を保てる
  - サーバーレスなアプリケーションが開発できる
    - サーバ管理が不要

---

- 各アプリケーションで利用したい共通機能のAPI化の場合
  - 個別に保守しやすい状態を保てる
  - 特定の技術へのロックインを避けることができる

---

### RESTと、RESTの設計原則

- 分散システムにおいて複数のソフトウェアを連携させるのに適した設計原則の一つ
- ざっくりいうと、具体的に状態を定義した情報のやり取りのこと
- RESTの元になった論文から「RESTアーキテクチャ要素」を紹介します

--

- クライアントサーバー
  - システムの依存関係を減らし、個別に保守しやすい状態を保つことに繋がる
  - サーバーのスケーラビリティ向上や、テストがしやすく

--

- ステートレス
  - リクエストに含まれた情報のみをもとにレスポンスが決まる
  - 状態を保持しないので、やり取りが1回ごとに完結する
  - 前のやり取りの結果に影響を受けないので、シンプルな設計にできる

- ⇔ステートフル
  - サーバー側のセッション情報を基にレスポンスが変化する

--

- キャッシュ
  - APIの返却データを再利用可能に
  - APIの呼び出し数を減らし、レイテンシーを短くすることも

--

- 統一されたインターフェース
  - あらかじめ定義、共有された方法でやり取りされること
    - 目的に応じてHTTPメソッドを使い分ける
      - POST（作成）／GET（取得）／PUT（更新）／DELETE（削除）
  - 全体的なシステム構成が簡素化に繋がる

---

### REST API以外のAPI

#### GraphQL

- Facebookで開発されたWeb APIの為のクエリ言語
- 1つのリクエストで関係しあうリソース群を柔軟に取得できる
  - REST APIにおいて複数の問い合わせが必要だったり、不要な情報まで取得してしまうといった問題を解消
- Web APIとして提供するGraphQL SchemaとQueryを実行するGraphQLクライアントが分離している

---

### gRPC

- Googleが公開したサービス間連携の為のRPCフレームワーク
  - RPC（リモートプロシージャコール）は、外部のプログラムに対し処理を依頼するための規約
  - Aサーバで実装しているメソッドをBサーバで叩くなんてことも

--

#### ProtocolBuffers

- gRPCはProtocolBuffersを利用してバイナリデータをやりとりする
  - ProtcolBuffersはIDL（インターフェース定義言語）
  - `*.proto`ファイルにAPIの期待する入力・出力形式を定義する
  - `*.proto`ファイルをコンパイルし、各言語用のコードを生成
  - gRPCクライアントとサーバを別言語で開発できる

---

### AWSマネージドでAPIを簡単にお試しするには？

- REST API
  - API Gateway + Lambda + DynamoDB
- GraphQL
  - AWS AppSync + DynamoDB

---

### まとめ

- クライアントサーバの分離により、スケーラビリティが向上
- システムの依存関係が減り、個別に保守しやすい
- アーキテクチャによっては、同一アプリケーション内で別言語で開発なんてことも

---

### 参考文献

- Software Design 2021年2月号
- [Architectural Styles and
the Design of Network-based Software Architectures／CHAPTER 5
Representational State Transfer (REST)](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
- [GraphQLとRESTの比較](https://hasura.io/learn/ja/graphql/intro-graphql/graphql-vs-rest/)
- [gRPCと従来のREST APIの比較](https://www.integrate.io/jp/blog/grpc-vs-rest-how-does-grpc-compare-with-traditional-rest-apis-ja/)
- [マイクロサービスバックエンドAPIのためのRESTとgRPC](https://www.slideshare.net/disc99_/apirestgrpc)
- [マイクロサービスが開発・運用コストの削減にどう貢献するか考えてみた件](https://qiita.com/takahashisansan/items/7470a14e45aee2b6739f)
