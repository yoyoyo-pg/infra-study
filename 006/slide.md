# 勉強会第六回：IaCについて

---

## はじめに

- 今回はIaC（Infrastructure as Code）についての紹介です。

---

## IaC（Infrastructure as Code）とは

- インフラのコード化のこと

---

## IaCのメリット

---

## AWSにおけるIaCツールの紹介

---

### CloudFormation

---

### CDK（Cloud Development Kit）

- CDKの場合は、記述量が少なくとも「よくあるパターン」として明示しない部分を補完して構築してくれるリソースも中には存在します。

---

### Terraform

---

## TerraformとCDKの違い

似たような記述内容でも、構築されるものが全く異なる場合がある為、その紹介です。

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

見た目上の宣言はVPCとパブリックサブネット1つですが、  
この場合はVPCとパブリックサブネットが3つと、インターネットゲートウェイも作成されます。

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