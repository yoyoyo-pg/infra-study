# 勉強会第八回：AWSのIAM（権限回りの仕組み）について

## はじめに

- AWSでは、IAM（Identity and Access Management）という仕組みを使い、AWSリソースに対するセキュアなアクセスを実現しています
- 具体例
  - AWSマネジメントコンソールへのログイン
  - ECSコンテナやLambdaから、S3やDynamoDBへのアクセス（読み書き）
  - Lambdaから、CloudWatchLogsにログの吐き出し
  - 開発用PCからCodeArtifactのリポジトリの参照
- 今回はIAMの概要と、どのような認証認可が実現できるかを説明します

## まとめ

- IAMロールは「力」を授けます
- 強大な「力」には、同時にリスクも伴います
- その為、適切な「力」を与える設計が必要となります

## 参考文献

[AWS初心者にIAM Policy/User/Roleについてざっくり説明する](https://dev.classmethod.jp/articles/iam-policy-user-role-for-primary-of-aws/)  
[IAM ロールの PassRole と AssumeRole をもう二度と忘れないために絵を描いてみた](https://dev.classmethod.jp/articles/iam-role-passrole-assumerole/)
