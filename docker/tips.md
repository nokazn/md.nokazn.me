# tips

## Multi Stage Build する

```dockerfile
# Install dependencies
FROM node:12.18.0-alpine as init-stage
ENV HOST="0.0.0.0"
WORKDIR /app/
COPY . /app/
RUN yarn --frozen-lockfile --production=false

# Development
FROM init-stage as dev-stage
RUN yarn dev

# Start
FROM init-stage as start-stage
RUN yarn build:server
EXPOSE ${SERVER_PORT}
CMD ["yarn", "start:server"]
```

```bash
# ターゲットを指定してイメージを作成
$ docker build --target dev -t node-app:latest
```

## 参考

[マルチステージビルドの利用 | Docker ドキュメント](https://matsuand.github.io/docs.docker.jp.onthefly/develop/develop-images/multistage-build/#stop-at-a-specific-build-stage)