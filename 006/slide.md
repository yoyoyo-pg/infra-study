# 勉強会第六回：IaCについて

---

## はじめに

- 今回はIaC（Infrastructure as Code）についての紹介です。

---

## IaC（Infrastructure as Code）とは

>サーバの環境構築やデプロイを行う際に用いる手順書をコードに落とし込み、そのコードに対応したツールを使って自動的に実行する仕組み
（WEB+DB PRESS vol.91 PerlでInfrastructure as Code）

- IaCを実現する上で、様々な種類のツールがあります。構成管理ツール（サーバを立ち上げる際に、予め用意しておいた設定ファイルに基づいてソフトウェアの設定を自動で実行できるソフトウェア）と呼ばれるChef、Ansibleといったツールから、クラウドサービスのデプロイに使われるTerraform、AWS CloudFormation等、様々なサービスが存在します。
- これらのツールは、DSL（Domain Specific Language：ドメイン固有言語）を用いて記述されます。
  - DSLはツールにより様々で、Ruby、Pythonといったプログラム言語の場合や、JSONやYAMLといった設定ファイルの場合、またはツール独自の言語の場合もあります。

---

## IaCのメリット

- 冪等性（何度同じ操作をしても、同じ結果が得られる）が担保されます。
  - 「以前作った構成をもう一度、別の環境に構築したい」「同じ設定値で複数のインフラリソースを構築したい」場合に、素早く構築を行う事が出来ます。
- コード化により、Git等のバージョン管理システムを利用できます。
  - 差分管理やプルリクベースでの構築が出来るようになる事で、「いつ」「誰が」「何の」変更を行ったかの管理がしやすくなります。
  - インフラリソースがより把握しやすい状態となるので、属人化を防ぐことにも繋がります。
  - 開発プロジェクトや採用技術によっては「IaC用のコード」と「プログラムのコード」を同一リポジトリで管理し、開発チームとインフラチームが共に変更内容を把握、管理出来る状態とする事も可能です。こちらは後述します。

## IaCのデメリット

- インフラリソースに対する理解に加え、IaCツール自体の学習コスト（ツールの特性、DSL等）がかかります。
  - ただしIaCツールによっては、普段から使い慣れているプログラム言語をDSLとして利用する事も出来ます。
- IaCで構築したリソースに対する手動の変更は推奨されないので、検証フェーズ等インフラに対して頻繁に変更が必要な場合は、却ってIaC化していると都合が悪い場合もあります。

---

## AWSにおけるIaCツールの紹介

- AWSのリソース操作においては、複数の手段を取ることが出来ます。
- AWS内のリソース操作の歴史や思想については、[こちら](https://speakerdeck.com/tomoki10/learn-aws-through-aws-cdk)のスライドが非常に参考になります。

---

### CloudFormation

- AWSが2010年に発表したサービスです。
- DSLはYAML、JSONです。
- それまではAWSのリソースを構築する際は手動またはAWSのSDKを用いた構築を実行する必要がありましたが、CloudFormationの登場により、複雑なシステムを繰り返し構築する事が可能となりました。
  - CloudFormationのテンプレート内に変数を埋め込む機能を使う事で、環境別の構築も可能となっていたりします。
- ただし、テンプレートが冗長になりやすいといった課題があります。

---

### CDK（Cloud Development Kit）

- 2019年からAWSで提供され始めたツールキットです。
- こちらのツールキットは「TypeScript、Python、JavaScript、Java、C#/.Net、Go」といった言語でインフラ定義をする事が出来ます。
  - プログラムの特性を生かし、例えば「テストコード」「型チェック」「条件分岐」「繰り返し」等を用いて、より効率よく構築を進める事が出来ます。
- CDKによるデプロイを実行すると、内部的にCloudFormationが利用され、AWSにデプロイする流れとなっています。
- AWSインフラの理解、AWS CDKの理解（+多少なりともCloudFormationの理解）、プログラム言語自体の理解が必要なため、そういった意味での学習コストは高いです。

#### コマンドの紹介

- `cdk init`・・・cdkの新規プロジェクトを作成します。
- `cdk deploy`・・・cdkのプロジェクトをAWS上に新規デプロイや更新をします。
- `cdk diff`・・・cdk上で更新（`cdk deploy`）をかける前に、現在AWS上にデプロイしているcdkプロジェクトとの差分を確認します。

---

### Terraform

- HashiCorp社により、2014年に発表されたサービスです。
- HCL（HashiCorp Configuration Language）と呼ばれる独自のDSLを用います。
- AWS以外のクラウドサービスにも複数対応しており、マルチクラウドのアプリケーションやAWS以外を使う場合は有力な選択肢となるツールです。
- [Terraformによる構築を試してみた記事](https://yoyoyo-pg.hatenablog.jp/entry/2023/06/08/225656)もあるので、宜しければ参照ください。

---

## TerraformとCDKの違い

- 似たような記述内容でも、構築されるものが異なる場合がある為、その紹介です。
- 今回は一例として、VPC（Virtual Private Cloud）とパブリックサブネットを両者のツールを使い構築しています。
  - VPC、サブネットについては[第二回](../002/slide.md)の勉強会で解説しているので、忘れた方は見てみてください。

### Terraformの場合

ほとんど宣言通りに、VPCとパブリックサブネットが1つ作成されます。

```tf:vpc.tf
resource "aws_vpc" "test_create" {
  cidr_block  = "17.0.0.0/16"
}

resource "aws_subnet" "test_create" {
  vpc_id     = aws_vpc.test_create.id
  cidr_block = "17.0.1.0/24"
}
```

### CDKの場合

見た目上の宣言はVPCとパブリックサブネット1つですが、この場合はVPCとパブリックサブネットが3つと、インターネットゲートウェイも作成されます。
CDKの場合はリソースを明示しなくとも、「よくあるパターン」を構築してくれる便利な面がある一方、不要なリソースが構築されないよう注意する必要もあります。

```typescript:vpc.ts
export class VpcStack extends Stack {
  constructor(scope: Construct, id: string, props?: StackProps) {
    super(scope, id, props);
    // vpc
    new Vpc(this, 'TestVpc', {
      cidr: '18.0.0.0/16',
      subnetConfiguration: [
        {
          cidrMask: 24,
          name: 'public',
          subnetType: SubnetType.PUBLIC,
        }
      ]
    });
  }
}
```

## 参考文献

2016 WEB+DB PRESS vol.91 PerlでInfrastructure as Code
2020 WEB+DB PRESS vol.119 インフラ構築をコード化でより効率的に
