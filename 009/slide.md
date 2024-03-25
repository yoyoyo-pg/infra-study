# Node.jsの紹介

## はじめに

- Node.jsとは、サーバーサイドJavaScipt実行環境です。
- React.jsやNext.js、Express.js等、今やNode.js等のサーバーサイドJavaScript実行環境はモダンなWeb開発において必須の存在です。
- 今回はNode.jsについて、概要と使用例を紹介していきます。

## 早速ここで疑問

- サーバーサイドJavaScript実行環境と呼ばれるものの、React.jsやVue.jsといったWebフロントエンドで利用されるのは何故？

## パッケージ管理の必要性

- React等を用いて大規模なアプリケーションを開発するとなると、様々なパッケージが必要で、各パッケージには依存関係があります。
- Node.jsはパッケージのインストールと整合性を管理する為に必要で、npm（Node Package Manager）がその解決を担っています。
- Javaの開発ではMavenやGradleといったビルドツールによるパッケージ管理をするので、それのJavaScript版だと理解して頂くと分かりやすいと思います。

参考：[React入門！！の前に知っておきたいNode.jsとJavaScriptの知識](https://qiita.com/rpf_nob/items/6823fb8728754386ef30)

## Node.jsでHTTPサーバを作成してみよう

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

## Next.jsのプロジェクトを作成してみよう

- ここでは、サンプルのNext.jsプロジェクトを作成しながらNode.jsのよくあるプロジェクトの構成について解説します

```powershell
npx create-next-app
cd .\my-app\ # ディレクトリ移動
npm run dev # Next.js
```

## package.json

## node_modules

## npxって何？

## おまけ

- Expressを用いたHTTPサーバ
  - [express-app](./express-app/index.js)
