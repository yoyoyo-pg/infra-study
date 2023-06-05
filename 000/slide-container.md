# 勉強会：コンテナ基礎

## はじめに

- コンテナを利用するメリットが理解できる状態
- コンテナイメージの作成から実行までの一連の流れをざっと理解できる状態

### 話すこと

- コンテナを理解する上での前提知識
  - linuxカーネルとディストリビューションについて
- コンテナ基礎
  - コンテナ is 何（従来の仮想化と比較しながら）
  - コンテナを利用するメリット
  - コンテナの注意点
- Dockerの基礎
  - Dockerコンテナのライフサイクル
  - Dockerイメージとは
  - Dockerイメージのビルド
  - Docker Hubについて
- AWSのコンテナ関連のサービスについて
  - ECS
  - Fargate
  - ECR
- おまけ：AWS Cloud9で試してみる

### 話さないこと

- Dockerを支える技術等（名前空間、仮想NIC、OverlayFS）
- Docker Compose（複数コンテナの定義と実行）
- Kubernetes関連（Kubernetes,EKS）

## コンテナを理解する上での前提知識

### linuxカーネルとディストリビューションについて

- **はじめに**
  - 狭義のlinux・・・カーネル
  - 広義のlinux・・・ディストリビューション

- **linuxカーネル**
  - OSの核となる部分。（メモリー管理、ファイル管理、デバイスドライバ、プロセス管理など）
  - ハードウェアやアプリケーションを制御する為の基本的機能を実装
  - カーネルだけではPCは動作しない

- **linuxディストリビューション**（distribution：配布物）
  - linuxカーネル + 各種ソフトウェア（コマンド、ライブラリ）
  - CentOS,Ubuntu,AmazonLinux,AlpineLinuxなど

ex.RedHat系（CentOS）だとyumコマンド、Debian系（Ubuntu）だとapt系コマンド

```bash
yum install <パッケージ名>
apt-get install <パッケージ名>
```

## コンテナ基礎

### コンテナ is 何

- ホストOS上に論理的な区画（コンテナ）を作り、アプリケーションを動作させるために必要なライブラリやアプリケーションを1つにまとめ、あたかも個別のサービスとして使う事ができるようにしたもの。（コンテナはホストOSの1プロセスとして動作します）

- 従来の仮想化との違い
  - 従来の仮想化は「OSの上に仮想化ソフト、仮想化ソフトの上にOS」
  - コンテナは「OSの上にコンテナエンジン、コンテナエンジンの上にアプリケーション」
  - **コンテナはホストOSのカーネルを利用し、ホストOSの1プロセスとして動作する**

### コンテナを利用するメリット

- 可搬性（ポータビリティ）
  - アプリケーションを構成するコンポーネントをパッケージングできる。

- メリットの一例
  - テスト環境と本番環境をコンテナ化する事で、ミドルウェアのバージョンやconfファイルの差異を防止。
    - 環境要因によるトラブルを回避することが可能
  - コンテナのイメージはレジストリを通して、さまざまな種類が提供されている。
    - 開発環境の構築が容易
  - CIの実行環境としても使える（AWS CodeBuild,CircleCIなど）

- オーバーヘッドの少なさ
  - コンテナのアプリケーションをデプロイする際、OSを含める必要がない。
- 従来の仮想化に比べ、使用するリソース（CPU,ディスク容量,メモリ使用量）が少なく済む&起動の速さ

### コンテナの注意点

- 異なるOSのシステムは動かせない

- ホストOSがCentOSの場合
  - 〇 Ubuntuイメージのコンテナを起動できる
  - ✖  Windowsイメージのコンテナは起動出来ない

- 補足
  - WindowsでもDocker Desktop（Dockerクライアントツール）を使えば、linuxのコンテナを起動する事が可能です。
  - Docker DesktopはWSL2(Windows Subsystem for Linux 2)を利用しています。
  - 売上高1000万ドルまたは従業員数250人以上の場合は有料なのと、入れ子の仮想化が出来ない点には注意が必要です。

- 異なるCPUのシステムも動かせない
  - Intel,AMDプロセッサ向けのイメージのコンテナはArmプロセッサのホストで動かせない。逆もしかり。 ※例外有

- カーネルに関わる操作は出来ない
  - たとえばsystemctlコマンドはコンテナ内で使えない（コンテナはホストOSから隔離された環境の為）
  - 特権コンテナ（非推奨）としてコンテナを起動させれば使えるには使える

- 1コンテナ1アプリケーション
  - アプリケーションを複数のコンテナに分離することで、水平スケールやコンテナの再利用を簡単にする。
  - たとえばwebサーバだけ増やしたい等

## Dockerの基礎

- コンテナ仮想化を用いたプラットフォーム。
- ホストOSにDockerをインストールして利用

### Dockerコンテナのライフサイクル

- イメージの取得、共有(docker pull／push)
  - リポジトリ(後述)からdockerイメージをpull or push
- コンテナ作成(docker create)
  - イメージからコンテナを停止した状態で作成
- コンテナ起動(docker run)
  - イメージからコンテナを生成し、コンテナで任意のプロセスを起動
- コンテナ停止(docker stop)
  - 起動しているコンテナを停止
- コンテナ再起動(docker start)
  - 停止しているコンテナを起動
- コンテナ削除(docker rm)
  - 停止しているコンテナを削除
- イメージ削除(docker rmi)
  - コンテナイメージの削除

### Dockerイメージとは

- Dockerコンテナの設計図のようなもの。
- イメージを基にコンテナを起動する

### Dockerイメージのビルド

- ベースとなるイメージに対して、何らかの機能を加えて、ユーザイメージ（自分独自のイメージ）を作り出すこと
- 手動でビルドする方法と、Dockerfileを使ってビルドする方法が存在
- たとえばCentOSイメージの上にApacheイメージを載せ、confファイルを編集といった形でイメージを作成していく

### Docker Hubについて

- Dockerイメージを配布（push）、取得（pull）する事ができるDocker公式のリポジトリサービス（<https://hub.docker.com/>）
- GitHub同様にパブリックリポジトリ、プライベートリポジトリが存在
- AWSにもECRというコンテナリポジトリサービス（後述）が存在

## AWSのコンテナ関連のサービスについて

### ECS

>Amazon Elastic Container Service (Amazon ECS) は、クラスターでコンテナの実行、停止、管理を簡単に行うことのできる、高度にスケーラブルで高速なコンテナ管理サービスです。

- いわゆるコンテナオーケストレーションサービスと呼ばれるものの1つです。
- 複数のDockerコンテナの管理をしやすくする為のツールです。
  - 起動タイプをEC2,Fargateから選択
  - タスク（ライフサイクルの同じ複数コンテナ）を何個動かすかを設定で定義
  - アプリケーションの負荷高時にサーバーを増やす等のスケーリング、ロードバランシング管理

### Fargate

>AWS Fargate Fargate はAmazon ECSで使用できるテクノロジーであり、サーバーやAmazon EC2インスタンスの クラスターを管理することなくコンテナを実行できます。Fargate を使用すると、コンテナを実行するために仮想マシンのクラスターをプロビジョニング、設定、スケールする必要はありません。

- サーバーレスでコンテナが実行できるコンピューティングエンジン
- EC2インスタンスの準備の必要がない
- 従来のwebサーバーと違い、SSH接続してログを確認しに行く、といった使い方はしない
  - 結果、コンテナ内の標準出力、標準エラー出力経由で外部（Clowd WatchやDatadog等）に送信するという形に
- コンテナ版のAWS Lambdaと例えると分かりやすいと思っています。

### ECR

>Amazon Elastic Container Registry (ECR) は、コンテナイメージやアーティファクトをどこからでも簡単に保存、管理、共有、およびデプロイできる、完全マネージド型のコンテナレジストリです。

- DockerHubのAWSバージョン
- ECSのタスク定義ではECR内のコンテナイメージを参照し、利用します。

## おまけ：AWS Cloud9で試してみる

### Cloud9とは

ブラウザでコードを記述、実行できるクラウドベースのIDE（統合開発環境）です。  
Cloud9を起動すると、EC2インスタンスが立ち上がります。  
最初からdockerエンジンがインストールされている為、コンテナを試す際に便利です。  
手元の環境を汚さずに環境構築できる＆費用も起動している時間分しかかからない為、個人開発にもオススメです。

### コンテナ開発の一例

AWS上でdockerコンテナの開発を進める場合、一例として以下の流れとなります。

- Cloud9でコンテナの動作検証をしながらdockerイメージを作成
- 作成したコンテナイメージをECRにプッシュ
- ECRのコンテナイメージを参照し、ECSでコンテナ起動

## 入門１：UbuntuコンテナでHello world

ubuntuコンテナをpullし、コンテナのechoコマンドを実行します。

```bash
docker run ubuntu /bin/echo 'Hello world'
```

## 入門２：NginxコンテナをrunしてパブリックIPから接続

### ①まずはDockerイメージをpull

```bash
docker pull nginx
```

### ②pullしてきたイメージを一覧で確認

```bash
docker images
```

### ③pullしてきたイメージをrun

```bash
docker run -d -p 80:80 --name testweb nginx
```

オプション

```bash
-d:バックグラウンドで実行
-p:コンテナのポート:80をホストOSのポート80にバインド（割り当て）
--name:コンテナ名
```

### ④動作しているプロセスを確認

```bash
docker ps -a
```

※以下のコマンドで、実行中のコンテナにアクセスする事も可能です。

```bash
docker exec -it testweb /bin/bash
```

### ⑤Cloud9のEC2インスタンスに対してパブリックIPでHTTP接続

nginxのウェルカムページが閲覧できます。

```bash
http://EC2のIPアドレス/
```

### ⑥testwebコンテナ停止

```bash
docker stop testweb
```

## 入門３：Dockerfileからtomcatイメージをビルドし、RUN時にwar展開

### ①/var/lib/docker配下にDockerfileを用意

tomcatをインストール＆wgetコマンドをインストールし、サンプルのwarをコンテナ起動時に展開するDockerfileです。

```Dockerfile
FROM tomcat:9.0
RUN apt-get update && apt-get install -y wget
WORKDIR /usr/local/tomcat/webapps/
RUN wget http://tomcat.apache.org/tomcat-9.0-doc/appdev/sample/sample.war
```

コマンド補足

```Dockerfile
FROM:ベースイメージを指定
RUN:コマンド実行
WORKDIR:作業ディレクトリ
```

### ②Dockerfileをビルド

tomcatのベースイメージを基にtomcat-sampleイメージを作成

```bash
docker build -t tomcat-sample:tag1 .
```

オプション

```bash
-t:イメージ名:タグ名
 . Dockerfileの位置を指定
```

### ③tomcat-sampleイメージをRUN

tomcatコンテナの8080ポートをホストOSの80番ポートにバインド

```bash
docker run -d -p 80:8080 tomcat-sample:tag1
```

### ④tomcatコンテナにデプロイされたwarを確認

```bash
http://EC2のIPアドレス/sample/
```

### 後片付け

dockerコンテナを削除

```bash
docker rm コンテナID
```

dockerイメージを削除

```bash
docker rmi imageID
```
