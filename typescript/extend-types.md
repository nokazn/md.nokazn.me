## 型を拡張するとき

## npm に `@types/foo` が存在しないとき

ドキュメントルート内 (`baseUr` 内) の任意のディレクトリに `mod.d.ts` を作成し、以下のように型定義を書いていく。`.d.ts` ファイルは型情報のみを含む。

```typescript
declare module 'foo' {
  export type 'bar': number;
}
```

このように、モジュール単位で型定義を拡張することをアンビエント宣言という。モジュール名の宣言部分にはワイルドカード (`*`) が使用できる。

```typescript
declare module '*.vue' {
  import Vue from 'vue';
  export default Vue;
}
```

## ローカルに定義された型の拡張

```typescript
declare module 'express-serve-static-core' {
  export interface Response {
    render<Options extends object>(
      view: string,
      options?: Options,
      callback?: (err: Error, html: string) => void,
    ): void;
  }
}
```

## グローバルに定義された型の拡張

```typescript
declare global {
  namespace Express {
    interface User extends { userId: string } {}
  }
}
```

`declare global` 内では、`export` しなくてもグローバルに宣言される。

### ちなみに

`typeRoots` は `/// <reference types='' />` のような Triple Slash directives を解決する際に有効なものらしい。
デフォルトでは `@typs/` が設定されており、`**/node_modules/@types/` 配下にある npm パッケージの型定義はビルド時にコンパイラが global に参照できる。global な型定義を持たせないライブラリの型定義をここに配置するのはおそらく正しくない。

## 参考

[typeRootsの誤解 -- TypeScriptで、npmからインストールしたパッケージに型定義ファイル (*.d.ts) が存在しない場合の正しい対処方法 - Qiita](https://qiita.com/tetradice/items/b89a5dd41fcebf96379e)
[typeRoots is not working? · Issue #12222 · microsoft/TypeScript](https://github.com/Microsoft/TypeScript/issues/12222#issuecomment-260417733)
[Ambient declaration with an imported type in TypeScript - Stack Overflow](https://stackoverflow.com/questions/45099605/ambient-declaration-with-an-imported-type-in-typescript)



