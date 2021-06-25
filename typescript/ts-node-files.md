# ts-node で自前の型定義ファイルを認識させる

```ts
declare module 'lighthouse' {
  function lighthouse(
    url: string,
    options: Record<string, unknown>,
    extendedOptions?: Record<string, unknown>,,
  ): Promise<{
    report: string;
    lhr: any;
  }>;

  export default lighthouse;
}
```

のような独自の型定義ファイルを書いたとき、`tsconfig.json` に `paths` を指定しなかった場合、tsc には認識されるが ts-node では認識されない。

`--files` オプションを有効化して、`includes`, `excludes` 等を読むようにすると型定義ファイルも呼んでくれる。

```json
{
  "ts-node": {
    files: true
  }
}
```
