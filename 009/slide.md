# Node.jsの紹介

## はじめに

- Node.jsとは、サーバーサイドJavaScipt実行環境です。
- React.jsやNext.js、Express.js等、今やNode.js等のサーバーサイドJavaScript実行環境はモダンなWeb開発において必須の存在です。
- 今回はNode.jsについて、概要と使用例を紹介していきます。

## 早速ここで疑問

- サーバーサイドJavaScript実行環境と呼ばれるものの、React.jsやVue.jsといったWebフロントエンドで利用されるのは何故？

## パッケージ管理の必要性

- React等を用いて大規模なアプリケーションを開発するとなると、様々なパッケージが必要で、各パッケージには依存関係があります。
- Node.jsはパッケージのインストールと整合性を管理する為に必要で、`npm`（Node Package Manager）がその解決を担っています。

> [!NOTE]
> `npm`をJava用語で例えるなら、MavenやGradleといったビルドツールによるパッケージ管理を行うようなものです。

参考：[React入門！！の前に知っておきたいNode.jsとJavaScriptの知識](https://qiita.com/rpf_nob/items/6823fb8728754386ef30)

## サーバーサイドJavaScript実行環境としてのNode.js

- 先程はパッケージ管理に対してフォーカスしましたが、勿論サーバーサイドJavaScript実行環境として、Webアプリケーションとして動作させる事も可能です。
- また、先程述べたReact等のWebフロントエンドの開発環境としても利用されています。

### サーバーサイドJavaScript実行環境としてのNode.jsの利用例

Node.jsを用いて、簡単なHTTPサーバを作成する事も可能です。

- Node.js（LTS）のインストール

```powershell
choco install nodejs-lts
node -v # Node.jsのバージョン確認
npm -v # npmのバージョン確認
```

- `server.js`の作成

```js:server.js
// RUN node server.js
// Server running at http://127.0.0.1:3000/
const http = require('node:http');
const hostname = '127.0.0.1';
const port = 3000;
const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});
server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

- サーバの起動

```powershell
node server.js
```

## Webフロントエンドの開発環境としてのNode.jsの利用例

以下、サンプルのNext.jsプロジェクトを作成しながらNode.jsのよくあるプロジェクトの構成について解説します（Node.jsはインストール済みとして後の手順を進めます）

```powershell
npx create-next-app
cd .\my-app\ # ディレクトリ移動
npm run dev # Next.js
```

- `npm run dev`実行後に以下表示がされます。

```powershell
> my-app@0.1.0 dev
> next dev

   ▲ Next.js 14.1.4
   - Local:        http://localhost:3000

 ✓ Ready in 2.4s
```

- `localhost:3000`へアクセスすると、Next.jsのアプリケーションがホストされている事が分かります。

## Next.jsのプロジェクト作成時に出てきた`npx`って何？

- 先程`npm`はパッケージ管理を担っていると説明しましたが、`npx`はパッケージ実行を担っています。
- `npx`はNode Package Executerの略です。
- `npx`を使う事で、プロジェクト内にインストールされていない機能を一時的にインストールして実行可能です。
  - 例えば今回だと、[create-next-app](https://www.npmjs.com/package/create-next-app)パッケージが一時的にインストールされ、実行されることでNext.jsのプロジェクトを構築するという形となっています。

> [!NOTE]
> `npx`をJava用語で例えるなら、MavenやGradleを環境にインストールしていなくとも使える、Maven WrapperやGradle Wrapperのようなものです。

## Next.jsのプロジェクトにある`package.json`って何？

- Node.jsプロジェクトの中心的な設定ファイルです。
- プロジェクトの情報や、依存するパッケージの情報が記載されています。
- Node.jsプロジェクトをGitHub等からpullしてきたとき、最初に`npm init`や`npm ci`といったコマンドを叩きますが、これにより`package.json`の情報を基に依存パッケージがプロジェクト内にインストールされます。

> [!NOTE]
> `package.json`をJava用語で例えるなら、依存関係を定義しておく`pom.xml`のようなものです。

## `package.json`によってどこに依存パッケージがインストールされる？

- プロジェクト内の`node_modules`ディレクトリに依存パッケージがインストールされます。
- `node_modules`ディレクトリは肥大化しがちなので、Gitリポジトリにはコミットしない（`.gitignore`に指定する）のが一般的です。

> [!NOTE]
> `node_modules`をJava用語で例えるなら、ローカルPC内の`.m2`リポジトリのようなものです。

## ローカルインストールとグローバルインストールについて

Node.jsを語る上で欠かせない、こちらの二つの概念についても解説します。

- ローカルインストールとは、グローバルインストールと比較して出てくるNode.jsのプロジェクトにおける概念です。
- `npm install パッケージ名`コマンドを実行するとローカルインストールとなり、Node.jsの特定のプロジェクト内でのみパッケージが利用可能となります。
  - これは、プロジェクト内の`package.json`に記録されます。
- `npm install -g パッケージ名`コマンドを実行するとグローバルインストールとなり、コンピュータ全体にインストールする事になります。
  - 一般的には、プロジェクトに依存しない開発者ツールはグローバルインストールします。

尚、上述した`npx`コマンドを使うとローカルインストールされたパッケージを実行する事が可能です。プロジェクト内で統一されたツールのバージョンを利用する場合にも、`npm`コマンドが利用されます。

## 広がるサーバーサイドJavaScript実行環境の世界

- 今回は紹介しきれなかったのですが、Node.js以外にもBunやDenoといったサーバーサイドJavaScript実行環境も存在します。
- また、Node.js上での開発支援ツールとして、モジュールバンドラーとしてWebpackや近年だとViteであったり、テストツールとしてJestやMocha、コーディングフォーマッターや静的解析としてPrettierやESLint等、様々なツールが充実しています。
- Node.jsはもはや単なる「サーバーサイドJavaScript実行環境」の枠組みを超えて、昨今のWebアプリケーション開発には欠かせない技術となりつつあります。
- この資料が、Node.jsやモダンフロントエンド開発の理解の一助になれば幸いです。
