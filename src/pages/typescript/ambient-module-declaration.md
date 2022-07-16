---
layout: ../../layouts/index.astro
---

# tsconfig.json の paths は特に書かなくていい

```ts
import '@google-cloud/functions-framework';

declare module '@google-cloud/functions-framework' {
  export type ExtendedEventFunction<T> = (
    message: { data: T },
    context: Parameters<EventFunction>[1],
  ) => ReturnType<EventFunction>;
}
```

という型定義の拡張があるとき、

```json
{
    "paths": {
        "@google-cloud/functions-framework": [
            "./src/types/@google-cloud/functions-framework",
            "./node_modules/@google-cloud/functions-framework/build/src/functions",
        ]
    }
}
```

とすると、`node_modules` の型定義が無視されてしまう。

```json
{
    "paths": {
        "@google-cloud/functions-framework": [
            "./node_modules/@google-cloud/functions-framework/build/src/functions",
            "./src/types/@google-cloud/functions-framework",
        ]
    }
}
```

とすると、`node_modules` の型定義が有効になり、自前の型定義拡張も有効になる。

ただ、`paths` を明示的に書かなくても暗黙的に型定義を辿ってくれるので問題ない。
