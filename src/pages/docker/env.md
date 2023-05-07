---
layout: ../../layouts/index.astro
---

# docker-compose での環境変数の扱い

## Dockerfile で `ENV` を指定した場合

```docker
FROM node:12.18.0-alpine as build
ENV PROJECT_WORKDIR=/app/
WORKDIR ${PROJECT_WORKDIR}
COPY . /app/
RUN yarn

FROM build as dev
WORKDIR ${PROJECT_WORKDIR}
```

`ENV` でコンテナのイメージ内に定義された環境変数は `Dockerfile` で参照できる。
また、multi stage build をしても、`ENV` で環境変数が設定されたイメージを引き継いだイメージ内でも同様に参照できる。

## `.env` ファイル内で環境変数を指定した場合

```yaml
# .env
PORT=3000
```

```yaml
# docker-compose.yml
version: '3.0'
services:
  client:
    build: .
    container_name: client
    ports:
      - ${PORT}:${PORT} # 3000:3000 でバインドされる
    environment:
      - PORT=4000 # コンテナ内では $PORT は 4000
```

`docker-compose.yml` 内ではホストマシン内の環境変数または `.env` ファイル内の環境変数が使用できる。
`services.<service name>.environment`  や `services.<service name>.env_file` 内で定義した環境変数は `docker-compose.yml` 内では参照できない。

```sh
$ docker-compose --build up client
$ docker inspect client
...
"PortBindings": {
    "3000/tcp": [
        {
            "HostIp": "",
            "HostPort": "3000"
        }
    ]
},
...

$ docker exec -it client /bin/sh
$ echo $PORT
4000
```

## ホストの環境変数を直接設定する場合

```yaml
# .env
PORT=3000
```

```yaml
# docker-compose.yml
version: '3.0'
services:
  client:
    build: .
    ports:
      - ${PORT}:${PORT} # 5000:5000 でバインドされる
    environment:
      - PORT=4000 # コンテナ内では $PORT は 4000
```

```sh
PORT=5000 docker-compose --build client
```

前項と同様のファイルを用意し、コマンド実行時にシェル変数を設定する場合を考える。

`docker-compose.yml` 内では`.env` 内の環境変数よりシェルで設定した変数が優先的に参照される。
ビルドされたコンテナ内では `services.<service name>.environment` で設定された環境変数に上書きされる。

```sh
$ docker-compose --build up client
$ docker inspect client
...
"PortBindings": {
    "4000/tcp": [
        {
            "HostIp": "",
            "HostPort": "4000"
        }
    ]
},
...

$ docker exec -it client /bin/sh
$ echo $PORT
4000
```

以下のように環境変数名 `PORT` のみを指定した場合はホストのシェル変数の値がそのまま引き継がれる。

```yaml
# docker-compose.yml
version: '3.0'
services:
  client:
    build: .
    ports:
      - ${PORT}:${PORT} # 5000:5000 でバインドされる
    environment:
      - PORT # コンテナ内では $PORT は 5000
```

```sh
$ docker exec -it client /bin/sh
$ echo $PORT
5000
```

## 参考

[Compose における環境変数 — Docker-docs-ja 19.03 ドキュメント](https://docs.docker.jp/compose/environment-variables.html)
