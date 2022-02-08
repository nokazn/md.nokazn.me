# Distributive Conditional Types

Conditional Types では Distributive な性質がある。

ジェネリクスで受け取った型引数が Union Types の場合、 Conditional Types でマッチしているか見る際に Union Types のそれぞれの場合ごとに分配して評価される。

```ts
// Union Types がそのまま保持される
type ToArrayWithoutDist<T> = T[];
// Conditional Types では distribute されてそれぞれが評価された結果の Union Types を返す
type ToArrayWithDist<T> = T extends any ? T[] : never;
// Conditional Types 内でも distributivity を防ぐ
type ToArrayAvoidingDist<T> = [T] extends [any] ? T[] : never;

type StrNumArr1 = ToArrayWithoutDist<string | number>; // (string | number)[]
type StrNumArr2 = ToArrayWithDist<string | number>; // => string[] | number[]
type StrNumArr3 = ToArrayAvoidingDist<string | number>; // => (string | number)[]
```

Conditional Types の `extends` キーワードの前後の型に対して分配法則が適用される。

```ts
type NonNullable<T> = T extends null | undefined ? never : T;

type Str = NonNullable<string | null>; // => string
type Str = NonNullable<string | undefined | null >; // => string
```

`never` 型は empty な Union Types としての側面もあり、Conditional Types で分配する際に `never` であることが決定してしまうので Conditional Types での評価がなされない。
 `never` 型にマッチさせたい場合は `ToArrayAvoidingDist` のようにする必要がある。
タプルの中で評価させることで、TypeScript コンパイラーに分配される前に評価するよう伝えることができる。

```ts
type InvalidIsNever<T> = T extends never ? true : false;
// expected true
type NeverType1 = InvalidIsNever<never>; // => never
type NeverType2 = InvalidIsNever<1>; // => false

type IsNever<T> = [T] extends [never] ? true : false;
type NeverType3 = IsNever<never>; // => true
type NeverType4 = InvalidIsNever<1>; // => false
```

## 参考

[TypeScript: Documentation - Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)
