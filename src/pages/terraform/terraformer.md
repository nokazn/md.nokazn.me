---
layout: ../../layouts/index.astro
---

# Terraformer で既存のリソースをコード化する

## Terraformer

## 手順

以下のファイルを作成しておく。

<!-- TODO: `.tf` ファイルのシンタックスハイライトを有効にする -->
<!-- markdownlint-disable MD001 MD040 -->
```
# provider.tf
provider "aws" {
  region = "ap-northeast-1"
}

terraform {
 required_providers {
  aws = {
     version = "~> 3.70.0"
  }
  }
}
```

```bash
# 初期化
$ terraform init

# import
$ terraformer import aws -r lambda,sqs,cloudwatch, --regions=ap-northeast-1 --path-pattern {output}/{provider}/
```

## 参考

- [Terraformerを使ってTerraformに既存インフラのリソースをインポートする | 株式会社ビヨンド](https://beyondjapan.com/blog/2020/05/terraformer-import-existing-infrastructure/)
- [Terraform v0.12.29 → v0.15.0にバージョンアップしました | ランサーズ（Lancers）エンジニアブログ](https://engineer.blog.lancers.jp/sre/versionup-terraform/)
  - provider が `aws` (deprecated) になっていたので、`hashicorp/aws` に書き換える必要がある
