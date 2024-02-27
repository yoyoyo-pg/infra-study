# SRE問題 - 参考資料

## AWSサービス

### ネットワーク関連

#### VPC（Virtual Private Cloud）

- 仮想ネットワークのサービス
- パブリックサブネット・プライベートサブネットを構築し、その中にALB,EC2,DBといったインフラリソースを配置する

[有志勉強会第一回](../001/slide.md)でも解説しています

#### CloudFront

- CDN（Contents Delivery Network）、コンテンツ配信ネットワークのサービス
- オリジンサーバ（例えばEC2やECS）の負荷軽減として、動画や画像といった静的コンテンツの配信に利用される

#### Route 53

- マネージドのDNS（Domain Name Systems）
- ドメインを登録し、対応したレコードを登録する事で名前解決をする
- メールの送信ドメイン認証（SPF・DKIM・DMARC）にも利用される

#### ALB（Application Load Balancer）

- アプリケーション層（L7）で機能するロードバランサ
- HTTPリクエストのパスによる振り分けや、負荷分散を実現している

#### NLB（Network Load Balancer）

- トランスポート層（L4）で機能するロードバランサ
- ALBと異なり、固定のIPをアタッチする事が可能

ALBとNLBは[有志勉強会第三回](../003/slide.md)でも解説しています

---

### コンピューティング関連

#### EC2（Elastic Compute Cloud）

- 仮想サーバのサービス
- Linux,Mac,WindowsなどをOSとして利用でき、CPUやメモリ、ストレージ等を選択しサーバを構築できる

#### Lambda

- サーバレスなコンピューティングを実現するサービス
- OSやミドルウェアの管理が必要ない
- 例えば動画の変換やZIP圧縮などWebサーバと非同期の処理や、APIとして使われる

#### ECS（Elastic Container Service）

- コンテナのオーケストレーション（管理）サービス
- Dockerと呼ばれる技術を用いて、サーバの仮想化を実現している
- OSやミドルウェアをEC2よりも意識することなく利用できる

---

### データベース、ストレージ関連

#### Aurora

- MySQLやPostgreSQLとの互換性を持つ、マネージドなRDBMS

#### DynamoDB

- Key-Value型のデータベースで、NoSQLとも言われる

#### S3（Simple Storage Service）

- データをオブジェクトとして保存するオブジェクトストレージサービス
- データバックアップや、ZIPやJARといったアーカイブ形式のファイル保存などに利用される

#### EBS（Elastic Block Store）

- ストレージボリューム
- HDD、SSDのような物で、EC2のデフォルトだとEBSがアタッチされる

#### EFS（Elastic File System）

- ファイルストレージサービス
- NFS（Network File System）として、例えばEC2のストレージとして利用される
