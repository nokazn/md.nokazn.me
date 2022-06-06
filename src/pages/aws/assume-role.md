# Assume Role して Terraform のコマンドを実行させるための設定

## 概要

- IAM ユーザーには Assume Role する最小限の権限だけ持たせる
- IAM ロール経由で Terraform のコマンドを実行するのに必要な権限を行使する

## 手順

以下のTerraform の実行に必要な権限をもったポリシーを Assume Role する IAM ロールに割り当てる。

### Terraform の実行に必要な権限

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEC2",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeAccountAttributes"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowCloudFront",
            "Effect": "Allow",
            "Action": [
                "cloudfront:GetCloudFrontOriginAccessIdentity",
                "cloudfront:GetDistribution",
                "cloudfront:ListTagsForResource"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowS3",
            "Effect": "Allow",
            "Action": [
                "s3:Get*",
                "s3:List*",
                "s3:PutObject"
            ],
            "Resource": "*"
        }
    ]
}
```

IAM ロールの信頼関係 (Trust Policy) は以下の用に設定する。

### Assume Role するロールの信頼関係 (Trust Policy)

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::<アカウント ID>:user/<ユーザー名>"
                ]
            },
            "Action": "sts:AssumeRole",
        }
    ]
}
```

MFA を有効化にしているユーザーにのみ Assume Role を許可する場合は信頼関係のポリシーに以下を追加する。

```diff
-           "Action": "sts:AssumeRole"
+           "Action": "sts:AssumeRole",
+           "Condition": {
+               "BoolIfExists": {
+                   "aws:MultiFactorAuthPresent": "true"
+               }
+           }
```

## 参考

- [IAM ロールの PassRole と AssumeRole をもう二度と忘れないために絵を描いてみた | DevelopersIO](https://dev.classmethod.jp/articles/iam-role-passrole-assumerole/)
- [Terraform用のAWSアクセスキーの権限管理を、スイッチロール形式で最適化する方法 | デロイト トーマツ ウェブサービス株式会社（DWS）公式ブログ](https://blog.mmmcorp.co.jp/blog/2021/01/27/terraform_with_assume_role/)
- [amazon web services - What's the most efficient way to determine the minimum AWS permissions necessary for a Terraform configuration? - Stack Overflow](https://stackoverflow.com/questions/51273227/whats-the-most-efficient-way-to-determine-the-minimum-aws-permissions-necessary)
