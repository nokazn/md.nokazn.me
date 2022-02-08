# Next.js の ISR を AWS で動かすなら

## 概要

- Next.js v11 以降で利用できる ISR を使う
- ISR を AWS で動作させたい

## レンダリング方法

### CSR (Client Side Rendering)

- クライアントサイドで初めてレンダリングを行う
  - サーバーから HTML を返却された時点では真っ白
- ファーストビューが若干遅れるのでパフォーマンス的にやや不利 (FCP)
- OGP 生成の問題

### SSR (Server Side Rendering)

- サーバーから完成された HTML を返却
- OGP は解決できる
- どこにホスティングするか

### SSG

- ビルド時にすべてのページを予め生成してしまう
- 任意のホスティングサービスに置ける
  - S3, Cloud Storage
- ビルドの度に全ページが生成されてしまう

### ISR

- CDN で実装されている stale-while-revalidate を Next.js の中で疑似的に再現したような機能
- 有効期限内であれば同じコンテンツを返し、有効期限が過ぎた後の1回目のリクエストが来たときに裏で静的ページをビルド
- SSR と SSG のいいとこどり
- 動かせる環境が限られる
  - Vercel で動かすのが楽

```tsx
import { GetStaticProps } from 'next'

export const getStaticProps: GetStaticProps<Props> = async () => {
  return {
    props: {
      timestamp: new Date().getTime(),
    },
    // 10秒ごとに更新
    revalidate: 10,
  },
};

export const Article = (props: Props) => {
  return <div>{timestamp}</div>
};
```

### DSG

- 差分があるファイルだけ静的ビルド (SSG) する
- [Gatsby がサポート](https://www.gatsbyjs.com/docs/how-to/rendering-options/using-deferred-static-generation/)しているらしい
  - 10/25 にリリースされた v4 から入っている

## 案

### コンテナ

- ECS on Fargate
- EKS

ISR するとコンテナごとにキャッシュができ、不整合が起きると思われる

### serverless-next.js (Lambda@Edge + S3 + CloudFront)

- Serverless Framework の
- <https://github.com/serverless-nextjs/serverless-next.js> に色々書いてある

![architecture](https://github.com/serverless-nextjs/serverless-next.js/raw/master/img/arch_no_grid.png)

| 種類                    | 説明                                           |
| ----------------------- | ---------------------------------------------- |
| ビルド用の静的ファイル  | S3 にホスティングされ、CloudFront で配信される |
| 静的ファイル (画像など) | S3 にホスティングされ、CloudFront で配信される |
|                         | SSR or SSG する                                |
| API 用ファイル          | Lambda@Edge 内で処理を実行                     |

```bash
$ npm i -g serverless # or yarn global add serverless
# 認証情報を設定
$ aws configure
```

```yaml
# serverless.yml
my-app:
    component: "./node_modules/@sls-next/serverless-component"
    inputs:
        bucketName: "serverless-next-${env.NODE_ENV}"
        bucketRegion: "${env.AWS_S3_BUCKET_REGION}"
```

#### デプロイ

- CI サーバーとかの中でコマンド実行すればデプロイできる

```shell
# serverless-next.js を使った場合は sls deploy じゃなくて sls だけでデプロイ
$ NODE_ENV=development AWS_S3_BUCKET_REGION=ap-northeast-1 sls
```

- Serverless Framerork のダッシュボードからリポジトリと連携すれば、自動デプロイできるらしい
  - 参考) <https://www.serverless.com/blog/announcement-cicd/>

![Announcing Serverless CI/CD](https://s3-us-west-2.amazonaws.com/assets.blog.serverless.com/2020-02-01-announcement-cicd/BranchDeploymentStatusEdit.png)
(公式ブログより)

#### Lambda@Edge

- Lambda を CloudFront のエッジロケーションで実行する

### Amplify

- Web アプリ・モバイルアプリの a<https://aws.amazon.com/jp/amplify/>
- [Next.js v11 がサポート](https://aws.amazon.com/jp/about-aws/whats-new/2021/08/aws-amplify-hosting-support-next-js-version-11/)されている

## さいごに

- Next.js には Vercel で動かすこと前提に作られている機能がいくつかあり、それらを他のクラウドサービスで動かすのは大変
