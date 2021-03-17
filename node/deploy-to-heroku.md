# Heroku にデプロイする際、依存ライブラリに devDependencies も含める

```sh
# buildpack かランタイムが devDependencies に依存する場合、プルーニングをスキップさせる必要がある
$ heroku config:set NPM_CONFIG_PRODUCTION=false YARN_PRODUCTION=false
```

