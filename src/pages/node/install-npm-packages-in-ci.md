---
layout: ../../layouts/index.astro
---

# CI 内でパッケージをインストールする

## 概要

依存パッケージをインストールする際、CI サーバー内で行う場合は lockfile が作られた時点のバージョンをインストールするよう指定したほうがよい。

## `npm`

```bash
npm ci
```

## `yarn`

```bash
yarn install --frozen-lockfile
```

### GitHub Actions でパッケージのインストールを行う例

```yaml
name: "deployment"
on:
  push:
    branches:
      - "main"
jobs:
  deploy:
    runs-on: "ubuntu-20.04"
    strategy:
      matrix:
        node-version:
          - "14.x"
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}
      - name: Install dependencies
        run: yarn install --frozen-lockfile


```

## 参考

- [yarnpkg - What is the closest to `npm ci` in yarn - Stack Overflow](https://stackoverflow.com/questions/58482655/what-is-the-closest-to-npm-ci-in-yarn)no
