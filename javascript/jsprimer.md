## 第一部: 基本文法

## 1.1 JavaScript とは

### 使われる場所

- Web ブラウザ
  - Web アプリケーションの GUI を作るのにほぼ必須
- Node.js
  - サーバーサイドで JavaScript で動く
  - Google V8 JavaScript エンジンで動く
- デスクトップ
  - Electron
- スマートフォンアプリ
  - React Native
  - Vue Native
- IoT
  - TODO: 組み込みデバイス用の Node.js 製フレームワークのようなものがあった気がするが思い出せない

### JavaScript と ECMAScript

- ECMAScript
  - JavaScript の中の共通の動作を定義した仕様 (実行環境に依らない)
  - Ecma International (https://www.ecma-international.org/) という団体によって運営されている TC39 という専門委員会によって策定されている
  - https://github.com/tc39/proposals で ECMAScript の API の提案がされてたりする
  - ES2015 で大変革があった
- JavaScript
  - ECMAScript に加えて実行環境に依る機能を合わせたもの

### JavaScript の特徴

- ルーツ
  - もともと Netscape Navigator というブラウザ用の言語として Brendan Eich が開発したもの
  - 1995年12月4日生まれ
  - C言語の文法に似た所謂 C 系といわれるものの一つで、Java , Scheme, Self などから影響を受けた
- Case Sensitive (大文字と小文字を区別)
- いくつかの予約語がある (`const` とか `return` とか)
- 文がセミコロンで区切られる
  - なくても JavaScript エンジンがよしなに入れて解釈してくれる
- strict mode という厳格なチェックを有効にするモードがある
- コンテクストによる違い
  - Script モード(strict じゃない) と Module モード (strict)
- 2015年以降は毎年仕様が更新される (ES2015, ES2016, ...)





## 1.2 コメント

- `//` １行コメント
- /* */ 複数行コメント
- `<!-- -->` HTML-like コメント
  - JavaScript を解釈しないブラウザで `<script>` タグ内のコードが表示されるのを防ぐハックとして HTML の形式のコメントで囲んでいた
  - **後方互換性**を考慮して ES2015 で追加された
      - ECMAScript の仕様の破壊的な変更が起こると、正しく表示できない Web ページが出てきてしまうため、後方互換性が非常に重視される

```js
// one liner comment
/*
multiple lines comment
multiple lines comment
*/
<!-- HTML-like comment -->
```



## 1.3 変数と宣言

- 変数宣言
  - `var`
  - `let` (ES2015~)
  - `const` (ES2015~)
- 変数をカンマで区切って複数個定義できる
- 前述のように後方互換性を考慮し、ES2015 では `var` の挙動を変更するのではなく 、新たに変数宣言子を追加された

### `const`

- 再代入不可 (オブジェクトのプロパティや配列の要素の変更は可)
- 可能な限り `const` 中心に

### `let`

- 再代入可
- 初期値を指定しなければ `undefiend` が設定される

### `var`

- 再代入・再定義可
- 巻き上げ (ホイスティング) の挙動
- ES2015 以降では可能な限り使用を避ける

### 変数名のルール

- `a-z, A-Z, _, $` が使用可能
- 数字から開始不可
- 予約語不可 (`const`, `return` など)
    - `let` や `const` は ES2015 以前から予約語として登録されており、既存のコードとの衝突の問題は発生しなかった

#### ps.予約語[^1] 

> await break case catch class const continue debugger default delete do else enum export extends false finally for function if import in instanceof new null return super switch this throw true try typeof var void while with yield

[^1]: [ECMAScript® 2022 Language Specification](https://tc39.es/ecma262/#sec-ecmascript-language-lexical-grammar) の12.6.2節



## 1.4 値の評価と表示

ここでの値の評価とは、入力した値を評価し、その結果を返すことをいう。

### 例

- `1 + 1` を評価して `2` を返す
- `const bookTitle = 'foo';` として `bookTitle` を評価したら代入された値を返す
    - `const bookTitle = 'foo';` 自体は文なので返り値はない

### ブラウザで JavaScript を実行する

#### ブラウザの開発者ツールのコンソールで実行する

Web ブラウザや、Node.js では REPL (Read-Eval-Print Loop) というインタラクティブにコードを実行・評価できる機能がある。

Windows の場合、Firefox では `F12` や `ctrl + shift + k`、 Google Chrome では `F12` や `ctrl + shift + i` で開ける。

```
>> 1
←  1
>> 1 + 1
←  2
>> 'foo' + 'bar'
←  "foobar"
>> [1] + [2]
←  "12"
>> const bookTitle = 'foo'; // no return value
←  undefined
>> bookTitle
←  "foo"
```

`shift + enter` で改行もでき、複数行のコードの評価も可能。REPL 実行は同じコンテクストの中で行われるため、`const` での複数回の変数宣言等はできない。

#### HTML ファイルから JavaScript を読み込む

ファイルの文字コードは `UTF-8`、改行コードは `LF` にして保存

```html
<!-- index.html -->
<html>
    <head>
        <meta charset="UTF-8">
        <title>Example</title>
        <script src="./index.js"></script>
    </head>
    <body>
    </body>
</html>
```

```js
// index.js
1;
```

として、`index.html` をブラウザで開くと開発者ツールのコンソールに `1` が表示される。

評価結果を表示するのは REPL 固有の機能で、ファイル内の JavaScript コードでは明示的にコンソールで表示する API を介して表示させる必要がある。

#### Console API

```js
// index.js
console.log(1); // => 1
console.log(1 + 1); // => 2
```

### コードの評価とエラー

- プログラムを実行しエラーが表示されても、エラーメッセージをちゃんと読めば解決できるので丁寧に読む
- エラーは構文エラーと実行時エラーとに分けられる

#### 構文エラー (Syntax Error)

JavaScript エンジンがコードを字句解析した後、構文上のエラーが存在しないかチェックされる。

```js
console.log(1; // => SyntaxError: missing ) after argument list
```

`Syntax Error` の内容がコロン以降に記され、エラーの発生したのがどのファイル・行番号・何文字目かが表示される。
ブラウザによっては (Firefox など) エラーのドキュメントへのリンクが出力されたりする。

#### 実行時エラー (Runtime Error)

構文上は問題ないが、プログラムの実行中に問題があり発生するエラー。標準でオブジェクトで規定されている実行時エラーにはいくつかの種類がある。

- Aggregate Error
    - 複数のエラーを1つにまとめる
    - `Promies.any()` の Promise が reject されたときなど
- (Eval Error)
    - `eval()` 内で発生したエラー
    - 現在は JavaScript からは使用されないが、後方互換性のため、`EvalError` オブジェクト自体はまだ存在している
- Internal Error
    - JavaScript エンジンで内部的に発生するエラー
- Range Error
    - 値が許容範囲内にない場合のエラー
    - 配列の範囲外アクセスなど
- Reference Error
    - 存在しない変数が参照されたときのエラー
- Type Error
    - 演算が実行できなくなった場合のエラー
    - 特に、値の型が期待されたものと異なるとき
- URI Error
    - グローバル URI 処理関数が間違った方法で使用された場合のエラー

実行時エラーは該当箇所を実行するまで判明しないことも多く、どこまで正常に動作しているかを確認する等の作業が必要になることが多い。 (デバッグ)

エラーメッセージで検索すると同様の問題の解決策がヒットすることも多い。

- [Google](https://google.com)
- [GitHub](https://github.com) の issues
- [Stack Overflow](https://stackoverflow.com/)



## 1.5 データ型とリテラル

JavaScript は動的型付け言語のため、変数の型はないが、変数内の値の型 (データ型) は存在

### データ型の分類

- プリミティブ型
    - 一度作成したデータ自体は変更できない (immutable)
- オブジェクト型
    - 一度作成したデータを変更できる (mutable)

#### プリミティブ型

- 真偽値: `true/false`
- 数値 (Number): 浮動小数点
- 巨大な整数 (BigInt): `9007199254740992n` など任意の精度の整数
- 文字列 (String): `"foo"` など
- `undefined`: 未定義の値
- `null`: 空の値
- シンボル (Symbol): 一意な値を表すデータ型で、`Symbol.iterator` のようにあるインターフェイスを満たすために必要なメソッドの識別子に使われたりする
    - cf. [Symbol.iterator - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator)

#### オブジェクト型 (複合型)

- 配列
- 関数
- 正規表現 (`RegExp`)
- `Date`
- `Map` / `WeakMap`
- `Set` / `WeakSet`
- オブジェクト (`{}` )
- etc...

`typeof` 演算子で値の種類をざっくりと調べられる。オブジェクトの種類までは調べられない。

```js
console.log(typeof 9007199254740992n); // => "bigint"
console.log(Symbol('foo')); // => "symbol"
console.log(typeof null); // => "object"  ← !?
```

### リテラル

プリミティブな値のほとんどや、一部のオブジェクトはリテラルで簡単に定義できる。

#### 真偽値 (Boolean)

```js
true; //=> true
false; // => false
```

#### 数値 (Number)

整数リテラル (`42`) と浮動小数点リテラル (`3.1415926535`) がある。内部的にはいずれもIEEE754の倍精度浮動小数点で表現されている。

##### 倍精度浮動小数点

単精度浮動小数点 (32bit で表す、C 言語の float) の倍のビット数で表現するため"倍精度"と呼ばれている。64bit 内以下のようにパラメータを振り分けている。

- 符号 (1bit): 正なら0、負なら1
- 指数 (11bit): 指数に127を加えたもの
- 仮数 (52bit): `1.xxx`のように指数を調整した内の`xxx`の部分

10進数で割り切れる小数でも２進数に変換すると循環小数になる場合があり (0.1 など)、演算の過程で丸め誤差が生じる場合がある。

```js
console.log(0.1 + 0.2); // => 0.30000000000000004
```

小数点以下の厳密な評価が求められる場合は注意が必要。

##### 整数リテラル

- 10進数
    - `0`, `2`, `999999` など
- 2進数
    - 先頭に `0b` をつける
    - `0b0`, `0b1`, `0b1001001` など
- 8進数
    - 先頭に `0o` をつける
    - `0`始まりでも表せたが、10進数と紛らわしいので新たに `0x`が導入された (現在は strict mode ではエラー)
    - `0o644`, `0o7142` など
- 16進数
    - 先頭に `0x` をつける
    - `0x30A2`, `0xFFFFFF` など

```js
// 評価されたときの表示は10進数
console.log(0xFF); // => 255
```

##### 浮動小数点リテラル

- `3.1415992`, `2e8` など
- `0.xxxx` は `.xxxx` とも書ける

#### BigInt (ES2020 以降)

IEEE754 で定められた倍精度浮動小数点では扱える桁の範囲は `2^53-1` となり、この範囲を超えると正しく表せない。
この範囲外の整数を安全に扱うために、任意の精度の整数を扱える BigInt 型が ES2020 で導入された。

```js
console.log(1n, typeof 1n); // => 1n "bigint"
// 2^53-1より大きな値も扱える
console.log(9007199254740992n); // => 9007199254740992n
// 小数は扱えない
1.2n; // => Uncaught SyntaxError: Invalid or unexpected token
```

#### Numberic Separators (ES2021 以降)

- 大きい桁の数値を読みやすくするためにアンダースコア (`_`)  を含めることができる
- リテラルの先頭、末尾、小数点や `e` の前後には付けることができない

```js
_123; // 変数として評価
// 下記はいずれも Syntax Error
3._14;
123_e10;
0xFF_;
1234n_;
```

#### 文字列 (String)

- "", '' で囲む
    - ダブルクォートもシングルクォートも基本的に同じ文字列
    - 文字列内にいずれかのクォートを含む場合は使われていないクォートで囲むか `\` で該当箇所をエスケープする
    - 改行するには改行コード (`\n`) を含める
- `` で囲む (テンプレートリテラル)
    - 改行可能
    - 文字列内で `${foo}` とすることで変数の値を埋め込める
    - 文字列内にクォートを含む場合は `\` でエスケープする

```js
'8 o\'clock';
"8 o'clock";
"複数行\nは\n改行コードで表現";

const foo = 1
`テンプレートリテラル
変数も埋め込める: ${foo}

`;
```

#### null リテラル

- 空値を表すリテラル
- cf. `undefined` はリテラルではなくただのグローバル変数で、書き換え可能

```js
// ES5 以前ではグローバル変数も書き換え可能 (ES5.1 以降は不可)
console.log(undefined); // => undefined
var undefined = 'foo';
console.log(undefined); // => 'foo'

// ローカル変数では現在も上書きできてしまう
function overwriteUndefiend() {
    var undefined = 1;
    conosle.log(undefined);
}
overwriteUndefined(); // => 1
```

`void` 演算子に任意の引数をとらせると常にグローバルの `undefiend` を返すため安全であるが、そもそも `undefined` の書き換えはやるべきでない。

#### オブジェクトリテラル

```js
const obj {
    // 文字列または Symbol がキーになりうる
    key1: 'value1',
    Symbol('key2'): 'value2',
    nested: {
        key3: 'value3',
    },
    123: 'value4',
};
// ドット記法
console.log(obj.key1); // => 'value1'
console.log(obj.123); // Uncaught SyntaxError: Unexpected number
// ブランケット記法
console.log(obj['key1']); // => 'value1'
console.log(obj['123']); // => 'value4'
```

#### 配列リテラル

- 任意の値に順序をつけて格納できるオブジェクトの一種
- インデックスは 0 始まり
- ブランケット `[]` の中にインデックスを指定して要素にアクセス

```js
const arr = [1, 2, 3, 4];
console.log(arr[0]); // => 1
```

#### 正規表現リテラル

- 正規表現を `/\d{1,2}/` のように囲んだもの
- `\` のように正規表現上で意味を持つ記号を文字列としてそのまま含めたい場合はエスケープする

### プリミティブ型とオブジェクト

- プリミティブ型の値をラッパーオブジェクトからコンストラクタを呼んで表すこともできる
- Symbol 以外のプリミティブ型は基本的にリテラルで表現する (Symbol はリテラル表現がない)
    - リテラルで表現してもオブジェクトのように、各々のプロパティにはアクセスできるため
    - 型変換を行うために用いる場合はある

```js
const str = new String('foo');
console.log(typeof str); // => "object"
console.log(str.length); // => 3
console.log(Number('1')); // => 1
```

# 疑問

- `console.log(typeof null); // => "object"` の歴史的な経緯
    - [history of typeof null](https://2ality.com/2013/10/typeof-null.html#:~:text=The%20%E2%80%9Ctypeof%20null%E2%80%9D%20bug%20is,lower%20bits%20of%20the%20units.&text=The%20data%20is%20a%2031%20bit%20signed%20integer.) という記事を昔読んだが、リンク切れになっていた
- `null` と `undefined` を明示的に使い分ける必要はあるか
    - オブジェクトのプロパティが設定されていない場合と、プロパティに明示的に `undefined` が設定されている場合を区別する場面？ (TypeScript を採用する場合はキーの有無が型レベル区別されるため、空値は `undefined` でも問題ない)
    - 個人的には区別せずに `undefined` で統一して、ライブラリで `null` が使用されている場合など、`== null` でまとめてチェックすることが多いです

```js
const obj = { key: undefined };
console.log(obj.key); // => undefined
console.log(obj.differentKey); // => undefiend
```



## 1.6 演算子

- 演算子: 演算を行う記号
- 被演算子 (オペランド): 演算の対象となる値

```js
1 + 2;
// + が演算子で 1, 2 がオペランド
```

### 二項演算子

`(左オペランド)(演算子)(右オペランド)` の形式

- プラス演算子 (`+`)
- 文字列結合演算子 (`-`)
- マイナス演算子 (`-`)
- 乗算演算子 (`*`)
- 除算演算子 (`-`)
- 剰余演算子 (`%`)
- べき乗演算子 (`**`) ← ES2016 から

```js
// 数値の加算と文字列の結合
console.log(1 + 3); // => 4
console.log('1' + '3'); // => 13

// 下記は同義
console.log(3 ** 2); // => 9
console.log(Math.pow(3, 2)); // => 9
```

### 単項演算子

`(オペランド)(演算子)` の形式で、何れもオペランドに数値をとる

- 単項プラス演算子 (`+`)
- 単項マイナス演算子 (`-`)
- インクリメント演算子 (`++`)
- デクリメント演算子 (`--`)

```js
console.log(+1); // => 1
// 実際は `praseInt` や `Number` コンストラクタを明示的に使う方がいい
console.log('+1'); // => 1
// 数値でな値に対しては NaN (Not a Number) を返却する
console.log(+'str'); // => NaN

console.log(-1); // => -1
console.log(-'1'); // => -1
console.log(-'str'); // => NaN

const num = 1;
// 後置 (評価して加算)
console.log(num++); // => 1
// 前置 (加算して評価)
console.log(++num); // => 3
// 前置 (減算して評価)
console.log(--num); // => 2
// 後置 (評価して加算)
console.log(num--); // => 2
console.log(num); // => 1
```

### 比較演算子

`(左オペランド)(演算子)(右オペランド)` の形式 (二項演算子の一種でもある)

### 厳密等価演算子 (`===`) / 厳密等価演算子 (`!==`)

オペランドの等価性を**厳密に**比較し、真偽値を返す。

```js
console.log(1 === 1, 1 === '1'); // => true false
// 参照先を評価する
console.log({} === {}); // => false
console.log([1, 2] === [1, 2]); // => false
```

### 等価演算子 (`==`) /  不等価演算子 (`!=`) 

オペランドの等価性を**ざっくり**比較し、真偽値を返す。
比較する前に暗黙的な型変換が行われ、予測しにくい挙動になることが多いので基本的に避けるべき。
`== null` は `null` と `undeifned` を1つの式でチェックできるため例外的に使われることがある。

```js
console.log(1 == 1); // => true
console.log(1 == '1'); // => true  ← !?
console.log(1 == [1]); // => true  ← !?!?
console.log(0 == [null]); // => true  ← !?!?
// 参照先を評価する
console.log({} == {}); // => false
```

### 不等号

- 小なり演算子 (`<`)
- 小なりイコール演算子 (`<=`)
- 大なり演算子 (`>`)
- 大なりイコール演算子 (`>=`)

### ビット演算子

- JavaScript のビット演算子ではオペランド (数値) を符号付32ビット整数として扱う
- 符号付32ビット整数は2の補数表現で表される
    - 2の補数表現: **桁内の最大値 + 1** を基準として、任意の数を引いたもので、それぞれのビットを反転させて 1 を足すと求められる
    - 範囲は (-2^31) ~ (2~31 - 1)

#### ビット論理積 (`&`)

AND 演算

```js
console.log(15 & 9); // => 9
console.log(0b1111 & 0b1001); // => 0b1001
```

#### ビット論理積和(`|`)

OR 演算

```js
console.log(15 | 9); // => 15
console.log(0b1111 | 0b1001); // => 0b1111
```

#### ビット排他的論理和 (`^`)

XOR 演算

```js
console.log(15 ^ 9); // => 6
console.log(0b1111 ^ 0b1001); // => 0b0110
```

#### ビット否定 (`~`)

各ビットを反転させた値を返す (1の補数表現)

```js
// n を否定演算すると -(x + 1)
conosole.log(~15); // => -16
console.log(~-15); // => -14

const str = '森林大十一';
// indexOf メソッドは該当箇所が見つからなかった場合 -1 を返し、~-1 は 0
if (~str.indexOf('木')) {
    console.log('木を見つけました');
}
// ES2015以降では includes メソッドが使える
if (str.includes('林')) {
    console.log('林を見つけました');
}
```

### 左シフト演算 (`<<`)

- 数値であるオペランドを指定されたn桁分だけ左へずらす (2^n倍するのと同義)
- 左にあふれたビットは破棄される

### 右シフト演算 (符号あり右シフト) (`>>`)

- 数値であるオペランドを指定されたn桁分だけ右へずらす (2^-n倍するのと同義)
- 右にあふれたビットは破棄される
- 最上位ビットで埋めていく

```js
console.log(9 << 2); // => 36
console.log(32 >> 3); // => 4
```

### 符号なし右シフト演算 (ゼロ埋め右シフト) (`>>>`)

- 右シフト演算を最上位ビットに関わらず、0 で埋めていく
    - 最上位ビットは常に0になるため、正の値を返す

```js
9 >>> 2; // => 2
-9 >>> 2; // => 1073741821
```

### 分割代入 (ES2015~)

プロパティ名と同じ名前の変数をスマートに代入できる

```js
const [first, second] = [1, 2, 3, 4, 5];
console.log(first, second); // => 1 2
const { key } = {
    key: 'value',
};
console.log(key); // => 'value'
```



### 論理演算子

falsey な値は以下で全部

- `false`
- `undefined`
- `null`
- `0`
- `0n`
- `NaN`
- `''`

上記以外はすべて truethy な値である。

- AND 演算子 (`??`)

    - 左辺が `true` なら右辺、左辺が `false` なら左辺を返す

    - 左辺が `false` の時点で右辺を評価せず左辺を返す

        - 左辺が truethy の場合のみ右辺を返す式を書きたいときに用いる (jsx 内とか)

        ```jsx
        return isLoggedin && <div>Logged in!</div>;
        ```

- OR 演算子 (`||`)

    - 左辺が`true `なら左辺、左辺が`false`なら右辺を返す
    - 左辺が`true`の時点で右辺を評価せず左辺を返す

- NOT 演算子 (`!`)

    - オペランドに暗黙的な型変換をして真偽値にした後、反転させる
    - 2回演算させると真偽値になる

- Nullish coalescing演算子 (`??`) (ES2020 以降)

    - 左辺が `undefined` と `null` (nullish な値) のとき、右辺の結果を返す

- 条件演算子 (三項演算子)

    - `(condition) ? (truethy なときの式) : (falsy なときの式)`
    - JavaScript では3つのオペランドをとる演算子は条件演算子のみなので、そのまま三項演算子と呼ばれることも多い
    - `if` 文は返り値をもたないため、式ベースで書きたいときに使える

- グループ化演算子 (`(`, `)`)

    - 演算子の優先順位を明示するために用いる

    - ```js
        const isStrOrNum = (typeof a === "string" && typeof b === "string") || (typeof x === "number" && typeof y === "number");
        ```

- カンマ演算子

    - 最後の式の評価結果のみを返す
    - 変数宣言時に複数まとめて宣言する際の `,` とは別物

    ```js
    1, 2, 3; // => 3
    ```

# 疑問

今回は特にありません



## 1.7 暗黙的な型変換

- 等価演算子 `==` で比較する際は、比較するオペランドの型を合わせてから比較される
- 比較する値の組み合わせの分だけパターンが存在するので、すべて覚えるのは現実的でないため、原則的に厳密な等価演算子 `===` を用いる

```js
console.log(1 === '1'); // => false
console.log(1 === false); // => false
console.log(10 === ['10']); // => false
```

```js
// 文字列の結合が優先される
console.log(1 + '2'); // => "12"
// 文字列に対して - の定義はないため、数値の減算が行われる
console.log(1 - '2'); // => -1

// 予測しずらい
const x = 1, y = '2', z = 3;
console.log(x + y + z); // => "123"
console.log(y + x + z); // => "213"
console.log(x + z + y); // => "42"
```

混乱を避けるためにも暗黙的な型変換は避ける。

#### 任意の値から真偽値の変換

```js
Boolean('string'); // => true
Boolean(1); // => true
Boolean(0); // => false
Boolean(''); // => false
```

- `Boolean`のコンストラクタ関数を通すと falsy な値では  `false`、それ以外の truethy な値では`true`が返る
- 否定演算子を`!!`として、2回用いても同じ結果が得られる

#### 数値から文字列の変換

```js
String(1); // => "1"
```



```js
String('str'); // => "str"
String(true); // => "true"
String(null); // => "null"
String(undefined); // => "undefined"
String(Symbol('foo')); // => "Symbol(foo)"
String({}); // => "[object Object]"
String([1,2,3]) // => "1,2,3"
String(() => {}); // => "() => {}"
```

オブジェクト型の変換は直感的でない挙動をするため避けるべき。

- 配列では `join()` メソッド
- オブジェクトでは `JSON.stringify()` メソッド

など組み込みメソッドを使用する。

### シンボル → 文字列

シンボル型はプリミティブ型であるが、暗黙的な型変換はできなず、行おうとすると例外を投げる

```js
'文字列と' + Symbol('シンボルの説明'); // => Uncaught TypeError: Cannot convert a Symbol value to a string
'文字列と' + String(Symbol('シンボルの説明')); // => '文字列とSymbol(シンボルの説明)'
```

### 文字列 → 数値

- `Number` コンストラクタが使用して、数値に変換できる

- `Number.parseInt()`, `Number.parseFloat()` のような組み込みメソッドも用意されている

- 変換に失敗した場合は `NaN` (Not a Number) が返される

    - `NaN` のデータ型としての扱いは Number 型になる
    - `Number.isNaN()` メソッドで `NaN` をチェックできる
    - 浮動小数点の仕様の標準化をしている IEEE754 では `NaN` はどの値とも等しくなることのない値とされている

    ```js
    console.log(NaN === NaN); // => false
    console.log(Number.isNaN(NaN)); // => true
    // 一度 NaN が混ざると以降の演算結果はすべて NaN になる
    console.log(NaN + 1); // => NaN
    ```

    

## 1.8 関数と宣言

### 関数宣言

```js
// 関数宣言
funtion 関数名(仮引数1, 仮引数2) {
    // 仮引数は関数内で扱える
    console.log(仮引数1);
    return 返り値;
    console.log('呼ばれない');
}

const 結果 = 関数名(引数1, 引数2);
console.log(結果); // => 関数の返り値
```

- 関数名
- 仮引数
- 関数の中身
- 関数の返り値
    - `return` された時点で関数呼び出し元に返り値が返却され、以降の処理は実行されない
    - 返り値なしで `return` した場合は、`undefined` が返却される

で構成される。

### 関数の引数

- 関数呼び出し時の引数の個数と関数定義時の仮引数の個数は異なってもよい
- 引数が省略されたときは仮引数が `undefined` であるとして扱われる

### 呼び出し時の引数が少ない場合

```js
function echo(x) { return x; };
console.log(echo(1)); // => 1
console.log(echo()); // => undefine
```

#### デフォルト引数 (ES2015 以降)

- 引数が渡されなったときのデフォルト値を設定できる
    - デフォルト引数導入以前は、OR 演算子 (`||`) でデフォルトを指定していたが、falsy な値を指定してもデフォルト引数が採用されてしまって不都合だった
        - ↑ に関しては ES2020 以降の Nullish coalescing 演算子 (`??`) を利用すれば防ぐことはできる

```js
function addPrefix(text, prefix = '[info] ') { return prefix + text; }
console.log(addPrefix('文字列')); // => '[info] 文字列'
console.log(addPrefix('文字列', '[error] ')); // => '[error] 文字列'
```

### 呼び出し時の引数が多い場合

```js
function add(x, y) { return x + y; }
add(1, 2); // => 3
// 余分な引数は無視される
add(1, 2, 3, 4, 5); // => 3
```

### 可変長引数

```js
// 引数の数の制限がない
console.log(1, 2, 3, 4, 5); // => 1 2 3 4 5
Math.max(1, 5, -100, 20); // => 20
```

内部的には `arguments` という関数内の特別な変数を参照して実装されている。

### Rest parameters (ES2015 以降)

可変長の仮引数を配列にまとめることができる

```js
function fn1(...args) { console.log(args); }
function fn2(arg1, ...args) {
    console.log(arg1);
    console.log(args);
}
fn1('a', 'b', 'c', 'd', 'e'); // => ['a', 'b', 'c', 'd', 'e']
fn1('a', 'b', 'c', 'd', 'e'); // => 'a' 'b' ['c', 'd', 'e']
```

cf. Spread 構文

- 配列やオブジェクトを展開することができる
- 配列を展開して、引数に渡すこともできる

```js
function fn(x, y, z) { console.log(x, y, z); }
const arr = [1, 2, 3];
fn(...arr); // => 1 2 3
```

### arguments

- `function` で定義された関数内で `arguments` という特殊な変数で引数の値がすべて格納された配列を参照できる
- 配列のようにインデックスでアクセスできるが、配列ではない 
    - 配列の組み込みメソッドは利用できない
- アロー関数では利用できない
- 仮引数を見て、可変長の引数を受け付けるのかが曖昧で、可読性が下がる

Rest parameters が利用できる (ES2015 以降) では特に使うべき理由がない

### 関数の引数と分割代入

関数の仮引数においても分割代入が使用できる

```js
function printUserId({ id }) { console.log(id); }
const user = { id: 42 };
printUserId(user); // => 42

function echo([first, second]) { console.log(first, second); }
const arr = [1, 2, 3];
echo(arr); // => 1 2
```

### 関数はオブジェクト

JavaScript の関数は第一級オブジェクトであり、値として代入したり関数自体を関数の引数にしたりできる。

```js
function fn() { console.log('fn is called'); }
const fn2 = fn;
fn2(); // => 'fn is called'
```

### 関数式

- 関数を値として代入した式を関数式という
- 関数式では関数名は省略できる
    - 関数名が省略された関数を匿名関数 (無名関数 / Anonymous function) という
- 関数式で関数名を定義しても外から呼び出すことはできない
    - 再帰させる場合などその関数内部からは呼び出せる

```js
const fn = function() { console.log('Anonymous function is called') }
fn(); // => 'Anonymous function is called'

const factorial = function innerFact(n) {
    return n === 0
        ? 1
        : n * innerFact(n - 1);
}
console.log(factorial(3)); // => 6
```

### Arrow Function (ラムダ関数) (ES2015 以降)

- 匿名関数を `function` キーワードなしで定義できる
- `this` を静的に決定できる
    - `function` を使うと呼ばれる場所によって `this` が変化する
- コンストラクタ関数になることはできない (`new` をつけて呼び出せない)
- `arguments` 変数を参照できない

```js
const fn1 = () => {};
const fn2 = (x, y) => { return x + y; };
// 1つの式を返り値とする場合はブロックを省略できる
const fn3 = (x, y) => x + y;

const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const evenNumbers = arr.filter((x) => x % 2 === 0); // => [2, 4, 6, 8, 10]
```

`function` で定義する関数の混乱を生みがちな挙動が Arrow Function では修正されている。

### 同じ名前の関数の上書き

```js
function fn() { console.log('first'); }
function fn() { console.log('second'); }
fn(); // => 'second'
```

アロー関数で定義された関数を `const` で宣言された変数に代入する場合は上書きできない。(再代入不可なので)

### コールバック関数

- 関数は第一級オブジェクトのため、引数の値にもなりうる (コールバック関数)
- コールバック関数を引数にとる関数・メソッドを高階関数という

```js
function 高階関数(コールバック関数) { コールバック関数() };
```

### メソッド

- オブジェクトのプロパティ内に定義されている関数
- JavaScript では関数とメソッドの間の機能的な差異はない

```js
const obj = {
    //  `function` キーワード
    method1: function () {},
    // アロー関数
    method2: () => {},
    // メソッドの短縮記法 (ES2015以降)
    method3() {},
};
```



## 1.9 文と式

### 式

- 評価された値をもつ
- 評価した結果を変数に代入できる (評価値)
- 文にもなれる (式文)

### 文

- 1つの手続き的な処理
- 評価した結果を変数に代入できない
- `;` で区切る

### ブロック文

- `{}` で囲むことでスコープを区切って文を書き連ねることができる
    - REPL で同じ名前の変数を宣言したい場合などに便利
- ブロックの末尾にセミコロンは不要

```js
{
    文;
    文;
}

// セミコロン不要
if (true) {
    console.log(true)
}
function fn () {}
```

### function 宣言 (文) と function 式

```js
// 文 (セミコロン不要)
function fn1() {}
// 式
const fn2 = function() {};
```



## 1.10 条件分岐

### if 文

```js
// ブロック
if (条件式) {
  文;
  文;
}
// 1文の場合はブロックなくても動く
if (条件式)
    文;
// ワンライナー
if (条件式) 文;
```

if の後の条件式は暗黙な型変換が行われた後、falsy か truethy かを判断している。

### else / else if 文

```js
const version = 'ES6';
if (version === 'ES5') {
    console.log('ECMAScript 5');
} else if (version === 'ES6') {
    console.log('ECMAScript 2015');
} else if (version === 'ES7') {
    console.log('ECMAScript 2016');
} else {
    console.log('???');
}
```

#### ネストした if

```js
const year = new Date().getFullYear();
if (year % 4 === 0) {
    if (year % 100 === 0) {
        if (year % 400 === 0) {
            console.log(`${year}年はうるう年`);
        }
    }
}
```

ネストが多いと可読性が下がるため、`else if ` / `else` を使ったり、Early return を使ったりしてネストを浅くするべき。

## `switch` 文

- 式のの評価値が指定した値かの場合分けを行うことができる
- `break` を省略した場合はマッチした条件があっても後続の `case` 節が実行される
- どの条件にもマッチしない場合、`default` 節内の処理を実行する
- `case` 節内で `return` する関数を書くとわかりやすい

```js
const version = 'ES6';
switch (version) {
    case 'ES5':
        console.log('ECMAScript 5');
        break;
    case 'ES6':
        console.log('ECMAScript 2015');
        break;
    case 'ES7':
        console.log('ECMAScript 2016');
        break;
    default:
        console.log('???');
        break;
}

const getVersion = () => {
    switch () {
       case 'ES5':
           return 'ES5';
       case 'ES6':
           return 'ECMAScript 2015';
      case 'ES7':
           return 'ECMAScript 2016';
       default:
           return '???';
    }
};
```



## 1.11 ループと反復処理

### `while` 文

- 条件内が truethy な間はブロック内の処理を繰り返す
- 条件式によっては無限ループが発生してしまうので取り扱いに注意する
    - 条件式のミスが発生要因のほとんど
    - 意図的にクライアントアプリケーションに無限ループを発生させると不正指令電磁的記録に関する罪に問われることもある

```js
let x = 0;
while (x < 10) {
    console.log(x);
    x += 1;
}
// => 0, 1, 2, ..., 9
```

### `do-while` 文

- `while` 文とほぼ同じだが、条件式の真偽に関わらず、少なくとも一度は処理が実行される

```js
let x = 100000;
do {
    console.log(x);
    x += 1;
} while (x < 10);
// => 100000
```

### `for` 文

- 繰り返す範囲を指定した反復処理を書く

```js
let total = 0;
for (let i = 0; i < 10; i += 1) {
    total += i + 1;
}
console.log(total); // => 55
```

```js
// for 文
const sum1 = (numbers) => {
    let total = 0;
    for (let i = 0; i < numbers.length; i++) {
        total += numbers[i];
    }
};
// 配列組み込みの forEach() メソッドを使う
const sum2 = (numbers) => {
    let total = 0;
    numbers.forEach((n) => {
        total += n;
    });
    return total;
};

const arr = [1, 2, 3];
sum1(arr); // => 6
sum1(arr); // => 6
```

#### `break` 文

- `switch` 内に出てきたものと同様に、現在のブロックのループを終了させる
- 多重になっているループではラベルをつけて脱出するループのブロックを明示できる

```js
const numbers = [1, 5, 10, 15, 20];
let isEven = false;
for (let i = 0; i < numbers.length; i += 1) {
    if (numbers[i] % 2 === 0) {
        isIven = true;
        break
    }
}
console.log(isEven); // => true

// 関数でやる場合は一時変数をなくし、Early return するとスマート
const isEvenIncluded1 = (numbers) => {
    for (let i = 0; i < numbers.length; i += 1) {
        if (numbers[i] % 2 === 0) {
            return true;
        }
    }
    return false;
};
// 配列組み込みの some() メソッドを使う
const isEvenIncluded2 = (numbers) => numbers.some((n) => n % 2 === 0);
```

### `continue` 文

- ループ内の現在の処理を中断するが、ループ自体は終了させず、次のループ処理を行う

```js
const filterEven1 = (numbers) => {
    const results = [];
    for (let i = 0; i < numbers.length; i += 1) {
        const num = numbers[i];
        if (num % 2 !== 0) {
            continue;
        }
        results.push(num);
    }
    return results;
};
// 配列組み込みの filter() メソッドを使う
const filterEven2 = (numbers) => numbers.filter((num) => num % 2 === 0);

const arr = [1, 5, 10, 15, 20];
console.log(filtereven1(arr)); // => [10, 20]
console.log(filtereven2(arr)); // => [10, 20]
```



### `for...in` 文

- `for...in` 文で取り出せるキーはスコープ内ごとに別々に宣言されるため、`const` で宣言できる
- いくつか問題のある挙動をする
    - 対象となるオブジェクトが継承しているオブジェクト内のキーも辿って列挙され、意図しない結果になることがある
    - 配列に用いた場合はインデックスが文字列で返ってくる

```js
const obj = {
    a: 1,
    b: 2,
    c: 3,
}
for (const key in obj) {
    console.log(key, obj[key]);
}
// "a" "1"
// "b" "2"
// "c" "3"

Object.keys(obj).forEach((key) => {
    console.log(key, obj[key]);
});
// "a" "1"
// "b" "2"
// "c" "3"

for (const key in ['a', 'b', 'c']) {
    console.log(key);
}
// "1"
// "2"
// "3"
```

### `for...of` 文

- JavaScript において、`Symbol.iterator` という Well-known-symbol のメソッドが実装されたオブジェクトを iterable と呼び、反復動作が可能
    - 配列、文字列、Map、Set、TypedArray などは反復処理可能
- `for...of` では iterable から値を1つずつ取り出して反復処理をさせている

```js
for (const value of [1, 2, 3]) {
    console.log(value);
}
// 1
// 2
// 3
```

### `let` でなく `const` で反復処理をする

`for` 文で出てきた合計値を求める処理は配列の `reduce` メソッドを用いて、一時変数を用いずに実装できる

```js
const sum = (numbers) => numbers.reduce((total, num) => total + num, 0);
```



## 1.12 オブジェクト

- キーとバリューのペア (プロパティ) の集合体
- プロパティのキーには文字列と Symbol (と数値) を指定できる
    - キーに数値を指定すると、内部的に文字列に変換される
- 配列や関数、Map、Set などもオブジェクトの一種
- 実行環境に予め取れこまれたオブジェクトをビルトインオブジェクトという
    - すべてのオブジェクトは `Object` というビルトインオブジェクトの元になっている

### オブジェクトを作成する

```js
// 空のオブジェクト
const obj1 = {};
const obj2 = {
    // キーのクォートは省略できる
    key: 'value1',
    // 変数名で使用できない文字を含む場合はクォートが必要
    'my-prop': 'value2'
};
const key = 'key';
// Computed property names
const obj3 = {
    [key]: 'value3';
}
```

### `{}` は `Object` のインスタンスオブジェクト

- `Object` は JavaScript のビルトインオブジェクト
- オブジェクトリテラル `{}` は `Object` のインスタンスオブジェクト

```js
// {} のようにリテラルで作るのと同じ
const obj = new Object();
console.log(obj); // => {}
```

### プロパティのアクセス

```js
const obj = {
    key: 'value1',
    'my-prop': 'value2',
    123; 'value3',
};
// ドットでアクセス
console.log(obj.key); // => 'value1'
// ブランケットでアクセス
console.log(obj['my-prop']); // => 'value2'
conosle.log(obj[123]); // => 'value3'
// ブランケットの中は変数も入れられる
const key = 'my-prop';
console.log(obj[key]); // => 'value2'
```

### 分割代入 (ES2015 以降)

```js
const obj = { key: 'value1' };
// 分割代入
const { key } = obj;
console.log(key); // => 'value1'
```

### プロパティの追加

- オブジェクトは作成された後もプロパティを変更できる (ミュータブル)
    - `Object.freeze()` されている場合は変更できない
- プロパティを指定して値を代入するとプロパティを変更できる
- 一度作成したオブジェクトのプロパティを変更すると混乱の元なので、あまりやるべきでない

```js
const obj = {};
obj.key = 'value';
console.log(obj.key); // => value
```

### プロパティの削除

```js
const obj = {
    key1: 'value1',
    key2: 'value2',
};
delete obj.key1;
console.log(obj); // => { key2: 'value2' }
```

### `const` で定義したオブジェクトは変更可能

```js
const obj1 = {};
// プロパティの変更 / 追加は再代入ではないので `const` で宣言された値でも変更可能
obj1.key = 'value';
// プロパティの変更 / 追加も禁止し、オブジェクトを凍結する
const obj2 = Object.freeze({ key: 'value' });
// strict モードでは例外を投げ、そうでなければ単純に無視される
obj2.key = 'value2';
```

### プロパティの存在を確認する

- JavaScript では存在しないプロパティへアクセスしても例外を投げず、`undeifned` を返す
    - プロパティに `undefiend` が設定されている場合と、キー自体が存在しない場合を区別できない
- 存在しないプロパティをネストしてアクセスしようとすると例外を投げる
    - `undefined` はオブジェクトではない

```js
const obj = {};
console.log(obj.key); // => undeifned
console.log(obj.key.key); // => Uncaught TypeError: Cannot read property 'key' of undefined
```

####  `in` 演算子

```js
const obj = { key: undefined };
if ('key' in obj) {
    console.log('`key` は存在する');
}
```

#### `hasOwnProperty` メソッド

```js
const obj = { key: undefined };
if (obj.hasOwnProperty('key')) {
    console.log('`key` は存在する');
}
```

### Optional chaining 演算子 (`?.`)

- `nullable` なプロパティに安全にアクセスするための演算子
- `nullable` だった場合、それ以降のプロパティのアクセスは行わず、`undefined` を返す

```js
const obj = {
    a: {
        b: 'nested',
    },
};
console.log(obj?.a?.b); // => 'nested'
console.log(obj?.dangerous?.dangerous); // => undefined
```

Optional chanining 演算子はオブジェクトのブランケット記法にも使える

```js
const obj = { 1: 'value' };
console.log(obj?.[1]?.['key']); // => 'value'
console.log(obj?.[2]?.['dangorous']); // => undefined
```

### `toString` メソッド

- オブジェクト自身を文字列化するメソッド
- `String` コンストラクタを呼ぶのと `toString()` メソッドを呼ぶのは同じ
    - `String` コンストラクタは内部的に `toString()` メソッドを呼んでいる

```js
const obj = {
    toString() { return 'my custom toString()' },
};
console.log(String(obj)); // => 'custom value'
```

### オブジェクトのプロパティは文字列化される

```js
const obj = {};
const keyObj1 = {};
const keyObj2 = {};
obj[keyObj1] = 1;
obj[keyObj2] = 2;
// 同じキーを指すので、上書きされている
console.log(obj); // => { "[object Object]": 2 }

// シンボルはキーにできる
const symbol1 = Symbol('1');
const symbol2 = Symbol('2');
obj[symbol1] = 1;
obj[symbol2] = 2;
console.log(obj); // => { "[object Object]": 2, Symbol(1): 1, Symbol(2): 2 }
```

### オブジェクトの静的メソッド (スタティックメソッド)

#### オブジェクトの列挙

- `Object.keys()`: プロパティ名の配列
- `Object.values()`: プロパティの値の配列
- `Object.entiries()`: プロパティ名とプロパティの値の配列の配列

#### オブジェクトのマージと複製

#### オブジェクトのマージ

- `Object.assign()` メソッド
- スプレッド構文でのマージ (ES2018 以降)

```js
const obj1 = { a: 1, c: 5 };
const obj2 = { a: 3, b: 2 };
// 第一引数は source で、残りは target を指定し、source にマージしたオブジェクトを返す
const merged1 = Object.assign({}, obj1, obj2); // { a: 3, b: 2, c: 5 }
// マージしたオブジェクトを新たに作成
const merged1 = { ...obj1, ...obj2 }; // { a: 3, b: 2, c: 5 }
```

### オブジェクトの複製

```js
const obj1 = { key: 'value' };
const obj2 = Object.assign(obj1);
const obj3 = { ...obj1 };
```

- 上記のコピーではオブジェクトの shallow copy が行われるため、ネストしているプロパティがあるとうまくコピーできない
- [lodash](https://github.com/lodash/lodash) の `cloneDeep` や、[just](https://github.com/angus-c/just#just-extend) の `extend` などのライブラリを用いるのがよい



## 1.13 プロトタイプオブジェクト

### `Object` はすべての元

- すべてのオブジェクト (`Array`, `String`, `Function`, ...) は `Object` を継承している
    - 正確には `Object.prototype` プロパティに定義された `prototype` オブジェクトを継承している
    - すべてのオブジェクトのベースとなるプロパティを定義したものを引き継いでいる
- 先述の `toString()` メソッドは `Object.prototype.toString()` にも定義されており、すべてのオブジェクトは `prototype` の `toString()` を引き継いだりオーバーライドしたりしている
- 継承元の `prototype` オブジェクトに定義されたメソッドをたどっていく仕組みをプロトタイプチェーンという

```js
const obj = { key: 'value' };
console.log(obj.toString === Object.prototype.toString); // => true
console.log(obj.toString()); // => "[object Object]"
```

### プロトタイプメソッドとインスタンスメソッドの優先順位

```js
const obj = {
    toString() {
        return 'custom toString()',
    },
};
console.log(obj.toString()); // => 'custom toString()'
```

#### `in` 演算子と `Object#hasOwnProperty` メソッドの違い

- `hasOwnProperty` はそのオブジェクト自身がプロパティを持っているか判定
- `in` 演算子は継承元を含めてプロパティを持っているか判定

```js
const obj1 = {};
console.log(obj.hasOwnProperty('toString')); // => false
console.log('toString' in obj); // => true

const obj2 = {
    toString() { return 'custom toString()'; },
};
console.log(obj.hasOwnProperty('toString')); // => true
console.log('toString' in obj); // => true
```

### オブジェクトの継承元を明示する `Object.create` メソッド

- `Object.create()` メソッドを用いると、継承元を第一引数に指定できる
- `Object.create(null)` は何も継承していないオブジェクト

```js
// const obj = {} と同義
const obj = Object.create(Object.prototype);
console.log(obj.hasOwnProperty === Object.prototype.hasOwnProperty); // => true
```

### Array も `Object` を継承している

- 配列のインスタンスは `Array.prototype` オブジェクトを継承しており、`Array.prototype` オブジェクトは `Object.prototype` オブジェクトを継承している (プロトタイプチェーン)
- 配列も `Object.prototye` に定義されているメソッドを利用できる

```js
const numbers = [1, 2, 3];
console.log(numbers.toString()); // => '1, 2, 3'
console.log(numbers.toString === Array.prototype.toString); // => true
console.log(numbers.toString === Object.prototype.toString); // => false
```

### `Object.prototype` を継承しないオブジェクト

```js
const obj = Object.create(null);
console.log(Object.hasOwnProperty); // => undefined
```

ES2015で `Map` が導入される前は連想配列として用いられていた。現在は素直に `Map` を使用する。



## 1.14 配列

- 要素に順序をつけて格納できるオブジェクト
- インデックスは0始まり

### 配列の作成とアクセス

```js
const empty = [];
const nums = [1, 2, 3];
const matrix = [
    [1, 2],
    [3, 4],
];

console.log(matrix[0][0]); // => 1
console.log(nums[nums.length - 1]); // => 3

// 疎な配列
const arr1 = [1,,, 3];
console.log(arr1.length); // => 4
console.log(arr1[1]); // => undefined
```

### オブジェクトが配列かどうか判定する

```js
console.log(Array.isArray({})); // => false
console.log(Array.isArray([])); // => true
// これは不適
console.log(typeof []); // => "object"
```

### `TypedArray`  (ES2015 以降)

- 通常の配列は可変長で、どの要素も格納できるが、`TypedArray` は固定長で型つきの配列を扱う
- WebGL やバイナリを扱う際にバイナリデータのバッファを表すために使われる

```js
const typedArray = new Int8Array(8);
console.log(Array.isArray(typedArray)); // => false
```

### 配列と分割代入

配列内のあるインデックスの値を変数として定義しなおせる。

```js
const arr = [1, 2];
const [first, second, third] = arr;
console.log(first, second, third); // => 1 2 undefined
```

### `undefined` の要素と未定義の要素の違い

```js
const arr = [1, undefined,, 3];
console.log(arr[1], arr[2]); // undefined undefined
```

区別するためには先述の `hasOwnProperty()` メソッドを用いる。

```js
const arr = [1, undefined,, 3];
console.log(arr.hasOwnProperty(1)); // true
console.log(arr.hasOwnProperty(2)); // false
```

### 配列から要素を検索

配列の要素を検索する際、

- 要素のインデックス
- 要素自体
- 要素が含まれるかという真偽値

が欲しい場合があるが、それぞれに対してビルトインのメソッドが用意されている。

#### インデックスを取得

- `Array#indexOf()`
    - 見つからない場合は -1 を返す
    - 要素自体を引数に渡して探索するため、プリミティブな値しか探せない
- `Array#findIndex()`
    - コールバック関数を渡して、要素を見つけるロジックを自由に書くことができる

#### 条件に一致する要素を取得

先述のように取得したインデックスから要素にアクセスすることもできるが、要素を直接取り出すメソッドも存在する。

```js
const colors = [
    { color: "red" },
    { color: "green" },
    { color: "blue" }
];
const blueColor = colors.find(({ color }) => color === 'blue');
const blueColor = colors.find(({ color }) => color === 'white');
console.log(blueColor, whiteColor); // { color: 'blue' } undefined
```

#### 指定範囲の要素を取得

配列の指定した範囲を切り出すのに、`Array#slice` メソッドが存在する。

```js
const arr = [1, 2, 3, 4, 5];
console.log(arr.slice(1, 4)); // [2, 3, 4]
console.log(arr.slice(1)); // => [2, 3, 4, 5]
console.log(arr.slice(-1)); // => [5]
console.log(arr.slice(1, 1)); // => []
console.log(arr.slice(4, 1)); // => []
```

#### 真偽値を取得

要素が含まれているかどうかを知りたい場合、 `Array#findIndex()` メソッドや、`Array#find()` メソッド、`Array#indexOf()` メソッドよりも意図を明確にできるメソッドとして、`Array.includes()` メソッドが存在する。(ES2016 以降)

```js
const arr = ['Java', 'JavaScript', 'Ruby', 'Kotlin', 'Swift'];
if (arr.includes('JavaScript')) {
    console.log('JavaScript is included');
}
```

`Array#some()` メソッドはコールバック関数にマッチする要素があるかどうかの真偽値を返す。

```js
const arr = ['Java', 'JavaScript', 'Ruby', 'Kotlin', 'Swift'];
if (arr.some((ele) => ele === 'JavaScript')) {
    console.log('JavaScript is included');
}
```

#### 追加と削除

```js
const arr = [1, 2, 3];
// 末尾に追加
arr.push(4);
console.log(arr); // => [1, 2, 3, 4];
// 末尾を削除
const popedItem = arr.pop();
console.log(arr, popedItem); // => [1, 2, 3] 4
// 先頭に追加
arr.unshift(0);
console.log(arr); // => [0, 1, 2, 3]
// 先頭を削除
const shiftedItem = arr.shift();
console.log(arr, shiftedItem); // => [1, 2, 3]  0
```

#### 配列の結合

```js
const arr = [1, 2, 3];
const arr1 = arr.concat([4, 5]);
console.log(arr1); // => [1, 2, 3, 4, 5]
const arr2 = arr1.concat(6);
console.log(arr2); // => [1, 2, 3, 4, 5, 6]
```

#### 配列の展開 (ES2015 以降)

- スプレッド構文で、配列リテラル中に既存の配列を展開できる
- `Array#concat()` メソッドと同等のことが行える

```js
const arr = [1, 2, 3];
const arrToAppend = [4, 5];
const arr1 = [...arr, ...arrToAppend];
console.log(arr1); // => [1, 2, 3, 4, 5]
const arr2 = [...arr1, 6]
console.log(arr2); // => [1, 2, 3, 4, 5, 6]
```

### 配列をフラット化 (ES2019 以降)

- `Array#flat()` メソッドで、多次元の配列をフラットな配列にすることができる
- 引数にフラット化する深さを指定することができる

```js
const arr = [[[1], 2], 3];
// デフォルトの深さは 1
console.log(arr.flat()); // => [[1], 2, 3]
console.log(arr.flat(1)); // => [[1], 2, 3]
console.log(arr.flat(2)); // => [1, 2, 3]
// すべての要素をフラット化
console.log(arr.flat(Infinity)); // => [1, 2, 3]
```

#### 配列から要素を削除

##### `Array#splice()` メソッド

指定したインデックスの要素を削除できる

```js
const arr = [1, 2, 3];
// インデックス1にある要素を1つ削除
arr.splice(1, 1); 
console.log(arr); // => [1, 3]
// すべて削除
arr.splice(0, arr.length);
console.log(arr); // => []
```

##### `length` プロパティへの代入

`length` プロパティへ長さを代入することで指定の長さへ変換することができる。

```js
const arr = [1, 2, 3];
arr.length = 0;
console.log(arr); // => []
```

##### 空の配列を代入

`let` や `var` で宣言された配列は、空の配列を再代入することでもともと参照されていた配列がメモリから解放され、空の配列に置換される。

```js
let arr = [1, 2, 3];
arr = [];
console.log(arr, arr.length); // => [] 0
```

#### 破壊的なメソッドと非破壊的なメソッド

- 破壊的なメソッド

    - 配列オブジェクトの内容そのものを変更する
    - 変更した配列そのものか、変更箇所を返り値として返す
    - 破壊的なメソッドを非破壊にするプロポーザルが存在し、現在は Stage1 (https://github.com/tc39/proposal-change-array-by-copy)

    | メソッド             | 返り値                     |
    | -------------------- | -------------------------- |
    | `Array#pop()`        | 配列の末尾の値             |
    | `Array#push()`       | 変更した配列の `length`    |
    | `Array#splice()`     | 取り除かれた要素を含む配列 |
    | `Array#reverse()`    | 反転した配列               |
    | `Array#shift()`      | 配列の先頭                 |
    | `Array#sort()`       | ソートした配列             |
    | `Array#unshift()`    | 変更後の配列の `length`    |
    | `Array#copyWithIn()` | 変更後の配列               |
    | `Array#fill()`       | 変更後の配列               |

    

- 非破壊的なメソッド

    - 配列オブジェクトのコピーを作成して、コピーを変更する
    - コピーし、変更された配列を返す
    - 副作用がないため、安全性が高まる

#### 配列を反復処理するメソッド

##### `Array#forEach()`

```js
const arr = [1, 2, 3];
arr.forEach((current, index, array) => {
    console.log(current, index, array);
});
// 1, 0, [1, 2, 3]
// 2, 1, [1, 2, 3]
// 3, 2, [1, 2, 3]
```

##### `Array#map()`

メソッドに渡したコールバック関数によって各要素を加工し、加工した新しい配列を返す。

```js
const arr = [1, 2, 3];
const doubleArr = arr.map((current, index, array) => current * 2);
console.log(doubleArr); // => [2, 4, 6]
```

##### `Array#filter()`

メソッドに渡したコールバック関数が返す真偽値によって新しい配列に要素を含むかを決め、フィルタリングした新しい配列を返す。

```js
const arr = [1, 2, 3];
const oddArr = arr.filter((currrent, index, array) => current % 2 === 1);
console.log(oddArr); // => [1, 3]
```

#### `Array#reduce()`

累積値と配列の要素を順番にコールバック関数へ渡し、畳み込んだ計算結果を返す。第二引数には初期値を渡せ、書略した場合は配列の最初の要素になる。

```js
const arr = [1, 2, 3];
const total = arr.reduce((accumulator, current, index, array) => accumulator + current, 0);
console.log(total); // 6
```

#### Array-like オブジェクト

配列のような振る舞いをするが、配列ではないオブジェクトを Array-like オブジェクトという。インデックスから要素へアクセスでき、`length` プロパティを持っているが、配列固有のメソッドはもっていない。
例えば、関数内で扱える `arguments` 変数はその一種である。

```js
function fn() {
    console.log(argumetns[0]);
    console.log(argumetns[1]);
    console.log(argumetns[2]);
    console.log(typeof arguments.forEach); // => 'undefined'
    const argumentsList = Array.from(arguments);
    console.log(typeof argumentsList.forEach); // => 'function'
}
```

#### メソッドチェーンと高階関数

配列を返すメソッドにさらにメソッドを結合させて、メソッド呼び出しを連続的に行うことができる。(メソッドチェーン)

```js
const ECMAScriptVersions = [
    { name: "ECMAScript 1", year: 1997 },
    { name: "ECMAScript 2", year: 1998 },
    { name: "ECMAScript 3", year: 1999 },
    { name: "ECMAScript 5", year: 2009 },
    { name: "ECMAScript 5.1", year: 2011 },
    { name: "ECMAScript 2015", year: 2015 },
    { name: "ECMAScript 2016", year: 2016 },
    { name: "ECMAScript 2017", year: 2017 },
    { name: "ECMAScript 2018", year: 2018 },
    { name: "ECMAScript 2019", year: 2019 },
    { name: "ECMAScript 2020", year: 2020 },
];
const versionNames = ECMAScriptVersions
    .filter((es) => es.year <= 2000)
    .map((es) => es.name);
```



## 1.15 文字列

### 文字列の作成

- ダブルクォート、シングルクォート、バッククォートで囲むことで文字列リテラルを利用できる。
- 任意のクォート記号で囲んだとき、同じクォート記号を使いたい場合はエスケープするか、別のクォート記号で囲む
- バッククォート内では、変数の埋め込み、改行が行える

```js
const double = "string";
const single = 'string';
const back = `string`;
console.log(double === single, single === back); true true

const multiline = `
1
2
3
`;
console.log(multiline); // '\n1\n2\n3\n'
```

#### エスケープシーケンス

```js
"\""; // => "
'\''; // => '
`\``; // => `
"\\"; // => \
"\n"; // => \n (改行)
"\t"; // => \t (タブ)
"\uXXXX"; // => Code Unit
"\u{X}"; // => Code Point
```

#### 文字列を結合する

```js
const str1 = 'a' + 'b';
console.log(str1); // => ab
console.log(str1 + 'c'); // => abc
console.log(`${str1}d`); // => abcd
```

#### 文字へのアクセス

文字列内の特定の位置へは配列のようにインデックスでアクセスできる。

```js
const str = 'abcde';
console.log(str[0]); // => a
console.log(str[1]); // => b
console.log(str[2]); // => c
console.log(str[10000]); // => undefined
```

#### 文字列とは

- コンピュータ上では、文字を対応するビット列に変換 (符号化 / エンコード) して保持している
- 文字コードには文字集合 (ここでは Unicode) と、符号化方式 (ここでは UTF-16) の二つの概念がある
- 文字と対応する ID の一覧表を符号化文字集合という
    - JavaScript は符号化文字集合に Unicode を採用している
        - 1文字を表す最小限のビットの組み合わせを Code Unit と呼ぶ
        - `String#charCodeAt()` メソッドで、指定インデックスの値の Code Unit を整数で返す
        - `String.charCodeAt()` メソッドでは Code Unit から文字列を生成できる
- 文字をエンコードする方式としては、JavaScript では UTF-16 を採用している
    - 文字集合内のそれぞれの ID をどのようなバイト列で表すかを定めたルール
    - Unicode の U+0000 ~ U+FFFF をそのまま16ビットで表現する
    - JavaScript の内部的な表現に UTF-16 を採用しているのであって、ソースファイルの文字コードが UTF-16 でなくても問題ない

```js
const str = 'アオイ';
// Code Unit を16進数で表示
console.log(str.charCodeAt(0).toString(16)); // => 30a2
console.log(str.charCodeAt(1).toString(16)); // => 30aa
console.log(str.charCodeAt(2).toString(16)); // => 30a4

// Code Unit から文字列へ変換
const str2 = String.fromCharCode(
    0x30a2,
    0x30aa,
    0x30a4,
);
console.log(str); // => 'アオイ'
```

#### 文字列の分解と結合

```js
const strs = 'Red, Blue, Green'.split(', ');
console.log(strs); // => ['Red', 'Blue', 'Green']
strs.join('・');
console.log(strs); // => 'Red・Blue・Green'

const strs2 = 'a   b c                 d'.split('/\s+/');
console.log(strs2); // => ['a', 'b', 'c', 'd']
```

#### 文字列の長さ

`Strings#length` プロパティが文字列の長さを返す。

```js
conosle.log('string'.length); // => 5
conosle.log(''.length); // => 0
```

### 文字列の比較

`===` (厳密な比較演算子) を用いる。

- Code Unit が同じ順番で並んでいるか
- 文字列の長さが同じか

で同じ文字列かどうか判定している。

```js
console.log('string' === 'string'); // => true
console.log('string' === 'strings'); // => false
```

`<`, `<=`, `>`, `>=` のような大小関係比較をする演算子で、Code Unit の大小関係を比較できるが、直感的でない結果が生まれることも多い。

```js
console.log('A'.charCodeAt(0)); // => 65
console.log('B'.charCodeAt(0)); // => 66
console.log('A' > 'B'); // => false
console.log('ABC' > 'ABD'); // => false
```

### 文字列の一部を取得

- 配列の `Array#slice()` メソッドのように、文字列を切り出すための `String#slice()` メソッドや、`String#substring()` メソッドが利用できる
- ともに開始位置と終了位置を引数にとるが、若干の挙動の違いがある
- いずれのメソッドも非破壊的なメソッド

```js
// String#slice()

const str = 'abcde';
console.log(str.slice(1)); // => 'bcde'
console.log(str.slice(1, 4)); // => 'bcd'
// 後ろからの位置
console.log(str.slice(-1)); // => 'e'
// 第一引数 > 第二引数 の場合は常に空文字を返す
console.log(str.slice(4, 1)); // => ''
```

```js
// String#substring()

const str = 'abcde';
console.log(str.substring(1)); // => 'bcde'
console.log(str.substring(1, 4)); // => 'bcd'
// マイナス値を指定すると、0として扱われる
console.log(str.substring(-1)); // => 'abcde'
// 第一引数 > 第二引数 の場合は引数が入れ替わる
console.log(str.substring(4, 1)); // => 'bcd'
```

引数にマイナス値を指定した場合や、第一引数 > 第二引数 の場合の挙動の違いは混乱を生むため、第一引数 < 第二引数かつマイナス値でない範囲で使用すると直感的に扱える。

### 文字列の検索

配列のときと同様、以下の値が欲しい場合について考える。

- 文字列のインデックス
- 文字列自体
- 文字列が含まれるかという真偽値

#### 文字列によるインデックスの取得

```js
const str = 'すもももももももものうち';
console.log(str.indexOf('も')); // => 1
console.log(str.lastIndexOf('も')); // => 8
```

#### 文字列にマッチした文字列の取得

文字列を検索してマッチした文字列は検索した文字列そのものになるため、自明である。

#### 真偽値の取得

- `String#startsWith()`: 先頭にあるか
- `String#EndWith()`: 末尾にあるか
- `String#includes()`: 含まれるか

### 正規表現オブジェクト

- 正規表現を用いることで、あるパターンにマッチする場合で検索することができる
- 次の文字は正規表現内で特殊な意味を持つ (特殊文字)

```
\ ^ $ . * + ? ( ) [ ] { } |
```

正規表現オブジェクト作成するには以下の方法がある

```js
// 正規表現リテラルから作成
const pattern1 = /パターン/g;
// コンストラクタから作成
const pattern2 = new RegExp('/パターン/', 'g');
```

### 正規表現リテラルと `RegExp` コンストラクタの違い

- 正規表現リテラル
    - ソースコードをパースした段階で正規表現パターンが評価される
- `RegExp` コンストラクタ
    - `RegExp` コンストラクタが呼び出されるまで正規表現パターンの評価はされない
    - 変数を埋め込む等、動的に正規表現パターンを生成したい場合に用いるとよい

```js
// main を呼び出さなくても例外が投げられる
function main() {
    const invalid = /[/;
}

// 引数によってマッチさせるスぺースの数を変更させる
function spaceRegExp(spaceCount) {
    // 文字列中で \ を有効するために \ 自体をエスケープする必要がある
    return new RegExp(`\\s{${spaceCount}}`);
}
```

### 正規表現による検索

- `String#indexOf()`: 指定した文字列にマッチした個所のインデックス
- `String#search()`: 指定された正規表現パターンにマッチした個所のインデックス

```js
const str = 'abcde12345fg';
const searchPattern = /\d{3}/;
console.log(str.search(searchPattern)); // => 5
```

### 正規表現によるマッチした文字列の取得

`String#search()` メソッドで正規表現パターンによって文字列を検索した場合、マッチした文字列の長さは可変なため、マッチした文字列の長さはわからない。

```js
const str = 'abcde12345fg';
const searchPattern = /\d{3}/;
// インデックス
console.log(str.search(searchPattern)); // => 5
```

そのため、マッチした文字列を返す `String#match()` メソッドや `String#matchAll()` メソッドが用意されている。

#### マッチした文字列の取得

```js
// マッチする場合
console.log('abcde12345fg'.match(/\d{3}/)); // => ["123", index: 5, input: "abcde12345fg", groups: undefined]
// マッチしない場合
console.log('abcde12345fg'.match(/\d{6}/)); // => null

// マッチするものすべてを検索する場合
console.log('abcde12345fg987'.match(/\d{3}/g)); // => ["123", "987"]
// マッチするものすべてを検索し、マッチした文字列ごとのイテレータを得る場合 (g フラグがない場合 TypeError を投げる)
const matched = 'abcde12345fg987'.matchAll(/\d{3}/g);
for (const match of matched) {
    console.log(match);
}
// => ["123", index: 5, input: "abcde12345fg987", groups: undefined]
// => ["987", index: 12, input: "abcde12345fg987", groups: undefined]
```

#### マッチした文字列の一部を取得

`String#match()` メソッドと `String#mtachAll()` メソッド内において、正規表現パターンに`()` を含ませた部分をマッチした部分からキャプチャすることができる。キャプチャした部分は、マッチした部分の情報を返す配列の2番目以降に`()`でキャプチャした分だけの文字列が格納される。

```js
const pattern = /ECMAScript (\d+)/;
const [all, caputured] = 'ECMAScript 6'.match(pattern);
console.log(all); // => 'ECMAScript 6'
console.log(caputured); // => '6'
```

### `RegExp#exec()` での `String#matchAll()`

ES2020 で `String#matchAll()` メソッドが登場するまで、`RegExp#exec()` メソッドが用いられた。

```js
const str = 'abcdef12345fg987';
const pattern = /[a-zA-Z]+/g;
console.log(pattern.exec(str)); // => ["abcdef", index: 0, input: "abcdef12345fg987", groups: undefined]
let matches;
while (matches = alphabetsPattern.exec(str)) {
    // `RegExp#exec`メソッドの返り値は`index`プロパティなどを含む特殊な配列
    console.log(matches);
}
```

- `RegExp#exec()` メソッドで `g` フラグがある場合は、マッチした文字列の情報を含む配列を返し、自らの `lastIndex` プロパティが更新され、次に `RegExp#exec()` メソッドが呼ばれた場合は `lastIndex` の位置から検索する
- `String#matchAll()` メソッドと違い、イテレータを返さないため、自前で繰り返し処理を書く必要がある

#### 真偽値を取得

`RegExp#test()` メソッドでパターンにマッチするかの真偽値を得ることができる。

```js
const str = 'すもももももももものうち';
// 'もも' で始まるか
console.log(/^もも/.test(str)); // => false
console.log(/も{5,}/.test(str)); // => true
// 'もも' で終わるか
console.log(/もも$/.test(str)); // => false
```

#### 文字列と正規表現どちらを使うべきか

##### 正規表現

- 曖昧な検索に強い
- コードの意図が伝わりにくい
    - コメントで正規表現でマッチさせたいパターンの説明等を書くべき

基本的に String のメソッドで表現できることは String のメソッドで表現し、柔軟な検索が必要な場合は正規表現を用いるとよい。

#### 文字列の置換 / 削除

- `String#replace()` メソッドを利用する
- 文字列はプリミティブなデータ型なので、データ自体から文字を削除することはできない
    - `delete` 演算子は使えない
    - strict mode では `delete` 演算子で削除できないプロパティを削除しようとすると例外を投げる

```js
// 文字列で置換
const str1 = 'cat';
str.replace('a', 'u'); // => 'cut'

// 正規表現で置換
const str2 = 'すもももももももものうち';
// 最初にマッチしたパターンが置換
str.replace(/も{3,4}/, 'モ'); // => 'すモもももものうち'
// マッチするパターンすべて置換
str.replace(/も{3,4}/g, 'モ')； // => 'すモモのうち'
```

ES2021 で追加された `String#replaceAll()` メソッドでは文字列による置換でも一致する要素すべてを置換できる。

```js
const str = '!!!';
// 文字列で置換
str.replace('!', '?'); // => '?!!'
str.replaceAll('!', '?'); // => '???'
// 正規表現で置換
str.replace(/\?/g, '?'); // => '???'
str.replaceAll(/\?/g, '?'); // => '???'
```

`String#replace()` メソッドと `String#replaceAll()` メソッドでは、第二引数に渡すコールバック関数内でキャプチャした文字列を利用して複雑な置換処理もできる。

```js
const toDateJa = (dateString) => {
    // 正規表現パターン内の () でキャプチャさせた文字列がコールバック関数の第二引数以降に渡される
    return dateString.replace(/(\d{4})-(\d{2})-(\d{2})/g, (all, year, month, day) => {
        return `${year}年${month}月${day}日`;
    });
};
toDateJa('今日は2021-05-19です。'); // => '今日は2021年05月19日です。''
```

#### 文字列の組み立て

単純な文字列の結合の場合では意図されていない不正な文字列が返る場合があるため、表記揺れ等を吸収する必要がある。
下記のような URL の操作は URL オブジェクトとして Web 標準の API として実装されている。

```js
const generateUrl = (baseUrl, pathName) => {
    return baseUrl + pathName;
};
const generateUrl2 = (baseUrl, pathName) => {
    // 末尾の / を削除し、表記揺れを吸収
    return baseUrl.replace(/\/$/, '') + pathName;
};

const baseUrl = 'http://example.com/';
const pathName = '/example.js';
generateUrl(baseUrl, pathName); // => 'http://example.com//example.js'
generateUrl2(baseUrl, pathName); // => 'http://example.com/example.js'
```

#### タグ付きテンプレート関数 (ES2015 以降)

`` で囲まれたテンプレートリテラルを関数に渡す際、() の有無によって渡される引数が変わる。

```js
const tag = (str) => { console.log(str); };
tag(`template ${0} literal ${1}`); // => 'template 0 literal 1'
```

() をつけずにテンプレートリテラルを引数を渡すと、第一引数にはテンプレートの中身を `${}` で split した配列、第二引数以降には `${}` の中に書いた式の評価結果が渡される。
このときの `tag` 関数をタグ関数 (Tag function) という。

```js
const tag = (strs, ...values) => {
    console.log(strs);
    console.log(values);
};
tag`template ${0} literal ${1}`;
// => ['template ', ' literal ', '']
// => [0, 1]
```

タグつきテンプレートを最終的に評価される文字列にするには以下のようにする。

```js
const stringRaw = (strs, ...values) => {
    // strs 内の要素を values 内の要素で結合していく
    return strings.reduce((res, str, i) => result + value[i - 1] + str);
};
console.log(stringRaw`template ${0} literal ${1}`); // => `template 0 literal 1`
// String#raw としてビルトインで提供されている
console.log(String.raw`template ${0} literal ${1}`); // => `template 0 literal 1`
```

タグつきテンプレート関数を利用することで、特定の形式に変換したデータを埋め込む処理が実現できる。
JavaScript 内に HTML などの別の言語や別の DSL を埋め込む際に利用されることが多い。

## 1.16 文字列と Unicode

- JavaScript での文字コード
    - 符号化文字集合: Unicode
    - エンコード方式: UTF-16
- 絵文字を含むような特殊な文字列を扱う際には、内部的なエンコード方式である UTF-16 を意識する場面がある

### Code Point

Unicode 上ですべての文字に割り当てられている一意な ID を Code Point (符号位置) という。

```js
// 文字の Code Point を求める
console.log('A'.codePointAt(0)); // => 65
console.log('あ'.codePointAt(0)); // => 12354
// Code Point から文字列生成
console.log(String.fromCodePoint(65)); // => 'A'
console.log(String.fromCodePoint(12354)); // => 'あ'

// 'あ' の Code Point を16進数で求める
console.log('あ'.codePointAt(0).toString(16)); // => '3042'
// 16進数の Code Point を文字列内で指定して文字列を作成
conosle.log('\u{3042}\u{3042}'); // => ああ
```

### Code Point と Code Unit の違い

- Code Point (符号位置)
    - Unicode 上で割り当てられた ID
- Code Unit (符号単位)
    - Code Point を UTF-16 でエンコード (変換) した後の値
    - ある範囲の文字では Code Point と同じ値になる

```js
// 文字列を16進数の Code Point の配列にして返す
const convertCodePoints = (str) => {
    return Array.from(str).map((char) => char.codePointAt(0).toString(16));
};

// 文字列を16進数の Code Unit の配列にして返す
const convertCodeUnits = (str) => {
    const codeUnits = [];
    for (let i = 0; i < str.length; i += 1) {
        codeUnits.push(str.charCodeAt(i).toString(16));
    }
    return codeUnits;
};

const str = 'ロケット🚀';
console.log(convertCodePoints(str)); // => ["30ed", "30b1", "30c3", "30c8", "1f680"]
console.log(convertCodeUnits(str)); // => ["30ed", "30b1", "30c3", "30c8", "d83d", "de80"]
// Code Units の長さを返す
console.log(str.length); // => 6
```

Unicode 上で `1f680` に割り当てられている文字を UTF-16 でエンコードすると `d83d`,  `de80` の4バイト (16ビット × 2 = 2 バイト × 2) で表される。

現在 Unicode 上では世界中の文字に ID が割り当てられ、その数は14万以上にものぼる。UTF-16 では16ビット (2^16 = 65536) ですべての文字を表すために Code Point に複数の Code Unit を組み合わせて割り当てられているものがある。(サロゲートペア)

### サロゲートペア

サロゲートペアでは2つの Code Unit の組み合わせで1つの文字 (Code Point) を表現する。UTF-16 では以下の範囲をサロゲートペアに利用する領域としている。

- `\uD800` ~ `\uDBFF`: 上位サロゲートペア
- `\uDC00` ~ `\uDFFF`: 下位サロゲートペア

```js
// サロゲートペアで表現される Code Unit
console.log('\uD83D\uDEB2'); // => '🚲'
// Code Point
console.log('\u{1f6b2}'); // => '🚲' 
// Code Unit の長さ
console.log('🚲'.length); // => 2
```

### Code Point を扱う

ES2015 から文字列を Code Point ごとに扱うメソッドが追加された。以下は文字列を Code Point ごとに扱う。

- `CodePoint` を名前に含む
- `u` (Unicode) フラグを有効化した正規表現パターン
- 文字列の iterator を扱うもの (分割代入、`for...of`、`Array.from()` メソッドなど)

### 正規表現の `.` と Unicode

正規表現で `u` (Unicode) フラグを有効にすると、文字列を Code Point が順番に並んだものとして扱う。

```js
// Code Unit ごとに1文字とカウントされる
const [all1, fish1] = '𩸽のひらき'.match(/(.)のひらき/);
console.log(all1); // => '\ude3dのひらき'
console.log(fish1); // => '\ude3d'
// Code Point ごとに1文字とカウントされる
const [all2, fish2] = '𩸽のひらき'.match(/(.)のひらき/u);
console.log(all1); // => '𩸽のひらき'
console.log(fish1); // => '𩸽'
```

### Code Point の数を数える

```js
console.log('🚲'.length); // => 2
console.log('\uD83D\uDF4E'); // => '🚲'
console.log('\uD83D\uDF4E'.length); // => 2
```

JavaScript では文字列の Code Point の個数を数えるメソッドは用意されておらず、自前で実装する必要がある。

```js
const codePoints = Array.from('ミカン🍊');
console.log(codePoints); // => ['ミ', 'カ', 'ン', '🍊'];
console.log(codePoints.length); // => 4
```

Code Point には視覚的に見えない制御文字なども定義されていて、Code Point を数えても直感的な値とは異なる場合がある。

### Code Point ごとに反復処理をする

```js
const countOfCodePoints = (str, codePoint) => {
    return Array.from(str).filter((item) => item === codePoint).length;
};
console.log(countOfCodePoints("🍎🍇🍎🥕🍒", "🍎")); // => 2
```

また、`for...of` での反復処理でも文字列を Code Point ごとに扱うことができる。

```js
const countOfCodePoints = (str, codePoint) => {
    let count = 0;
    for (const item of str) {
        if (item === codePoint) {
            count += 1;
        }
    }
};
console.log(countOfCodePoints("🍎🍇🍎🥕🍒", "🍎")); // => 2
```



## 1.17 ラッパーオブジェクト

JavaScript のデータ型はプリミティブ型とオブジェクト型に分けられるが、プリミティブなデータである文字列リテラルもオブジェクトと同じようにインスタンスメソッドが利用できる。

```js
'string'.toUpperCase(); // => 'STRING'
```

### プリミティブ型とラッパーオブジェクト

プリミティブなデータでは、真偽値 (Boolean)、数値 (Number) 、文字列 (String)、シンボル (Symbol) に対応するオブジェクトが存在し、このオブジェクトからもインスタンスを作成できる。

```js
const str = new String('string');
console.log(str.toUpperCase()); // => STRING
```

このようにインスタンス化されたものはプリミティブ型の値をラップしたオブジェクトといえる。(ラッパーオブジェクト)

| ラッパーオブジェクト | プリミティブ型 | 例              |
| -------------------- | -------------- | --------------- |
| `Boolean`            | 真偽値         | `true`, `false` |
| `Number`             | 数値           | `1`, `2`        |
| `String`             | 文字列         | '文字列'        |
| `Symbol`             | シンボル       | Symbol('説明')  |

```js
console.log(typeof 'str'); // => 'string'
console.log(new String('str')); // => 'object'
```

### プリミティブ型の値からラッパーオブジェクトへの自動変換

JavaScript ではプリミティブ型の値にプロパティアクセスする際、自動で対応するラッパーオブジェクトに変換され、ラッパーオブジェクトのメソッドが呼び出せるようになる。

逆に、ラッパーオブジェクトから作成した値を`valueOf()` メソッドで元のプリミティブな値を取り出すことができる。

```js
const wrapper = new String('str');
console.log(typeof wrapper); // => 'object'
console.log(typeof wrapper.valueOf()); // => 'string'
```

プリミティブな値とラッパーオブジェクトで作成した値を使い分ける利点は特にないため、リテラルからプリミティブな値を生成すべきである。	

## 1.18 関数とスコープ

関数の振る舞いを理解するにあたってスコープの理解は重要。

### スコープとは

- 変数や関数などの参照できる範囲を決めるもの
- スコープ内の変数はスコープ内からのみ参照でき、外部からは参照できない
- スコープ外の変数を内側から参照することはできる

```js
const fn = () => {
    const x = 1;
    console.log(x); // => 1
};
fn();
console.log(x); // Uncaught ReferenceError: x is not defined
```

関数の仮引数も関数のスコープに紐づけられる。

```js
const fn = (arg) => {
    console.log(arg);
};
fn(1); // => 1
console.log(arg); // => Uncaught ReferenceError: arg is not defined
```

関数によって作られるスコープを関数スコープという。
`let` や `const` で宣言される変数は同じスコープ内では一度しか宣言できないが、`var` や `function` による変数 / 関数の宣言は何度でも行え、上書きされる。

### ブロックスコープ

`{`  と `}` で囲まれた範囲をブロックといい、ブロックもスコープを作成する。(ブロックスコープ)

```js
{
    const x = 1;
    console.log(x); // => 1
}
console.log(x); // => Uncaught ReferenceError: x is not defined
```

`if` 文や `for` 文、`while` 文などもブロックスコープを作成する。反復処理ではループごとにブロックスコープを作成する。

```js
// ブロックスコープごとにスコープが作成される、`ele` はスコープごとに宣言されるため `const` で宣言できる
for (const ele of [1, 2, 3, 4, 5]) {
    console.log(element);
}
console.log(element); // Uncaught ReferenceError: element is not defined
```

### スコープチェーン

以下のように内→外へ変数や関数が定義されているか探索する仕組みをスコープチェーンという。

- 変数や関数を参照する際、自分のスコープから外側に向けて順に参照先を探し、一番近いものを参照する
- 自分のスコープの内側の変数や関数は見えない (参照できない)

```js
const x = 0;
{
    const x = 1;
    {
        const x = 2;
        {
            console.log(x); // => 2
        }
    }
    console.log(x); // => 1
}
console.log(x); // => 0
```

### グローバルスコープ

- プログラム直下に書かれたコードは、プログラム実行時に暗黙的に作成されるグローバルスコープに属する

- グローバルスコープ内の変数はグローバル変数といい、どのスコープからも参照できる

- グローバルスコープ内にはプログラム実行時に自動的に作成されるビルトインオブジェクトが存在し、どのスコープからも参照できる

    - ECMAScript で定義されている `undefined` のような変数 (`undefined` はリテラルではない) や、`isNaN` のような関数、`Array` や `RegExp` のようなコンストラクタ関数 (どの環境にも存在)
    - 実行環境 (ブラウザ、Node.js) によって変わる、 `document` や `window` (ブラウザ)、`module` (Node.js) など

- ビルトインオブジェクトと自分で定義したグローバル変数で同名のものがある場合、グローバル変数が優先される (変数の隠蔽 (shadowing))

    - `undefined` を書き換えてしまうのと同様、混乱の元になるのでビルトインオブジェクトを書き換えないようリンター等で防ぐ
    - 関数等を活用してスコープを小さく切り、大きいスコープでの変数定義を減らすことで影響範囲を狭めるとよい

    ```js
    const Array = 1;
    console.log(Array); // => 1
    console.log(new Array(3).fill(undefined)); // => Uncaught TypeError: Array is not a constructor
    ```

### 変数を参照できる範囲を小さくする

- 変数の参照できる範囲を狭めることでプログラムを安全にすることができる
    - 現在のスコープを参照するつもりがグローバル変数を参照してしまう (またはその逆) といった事例が防げる
- 変数は利用する範囲のスコープ内に留めておくとよい

### 関数スコープと `var`  の巻き上げ

`let` と `const` による宣言はスコープ内で一度きりで、`const` で宣言された変数は再代入できない。ES2015 以降では基本的に `const` で宣言し、再代入したい場合は `let` を使用する方針で問題ないが、既存のライブラリでは `var` 宣言されたコードが書かれている場合もある。

#### `let` と `var` の共通点

- 変数宣言時に初期値を設定しない場合は `undefinde` が割り当てられる
- 宣言後に値を (再) 代入できる

```js
let letX;
var varX;
console.log(letX, varX); // => undefined undefined
let letX = 'declared by let';
let varX = 'declared by var';
```

#### `let` と `var` の相違点

- `let` では宣言前に変数を参照すると `ReferenceError` を投げるが、`var` では宣言前に変数を参照しても `undeifned` になるだけで例外を投げない
    - 変数宣言を宣言と代入に分けて考えたとき、`var` による変数宣下では、宣言が暗黙的に**最も近いスコープやグローバルスコープ**の先頭に巻き上げられ、代入はコードを書いた箇所で実行されるという動作をする

```js
console.log(x); // => undeifned
var x = 'declared by var';
console.log(x); // => 'declared by var'
```

のようなコードは、

```js
var x;
console.log(x); // => undeifned
x = 'declared by var';
console.log(x); // => 'declared by var'
```

のように解釈されていると考えることができる。また、スコープについても、

```js
function fn() {
    console.log(x); // => undefined
    {
        var x = 'declared by var';
    }
    console.log(x); // => 'declared by var'
}
fn();
```

のようなコードは、

```js
function fn() {
    var x;
    console.log(x); // => undefined
    {
        x = 'declared by var';
    }
    console.log(x); // => 'declared by var'
}
fn();
```

のように解釈されていると考えることができる。このような挙動を**巻き上げ** (hoisting) という。この挙動はプログラムを予測しずらくするため、`const` や `let` で変数宣言を行うのが望ましい。

### 関数宣言と巻き上げ

`function` キーワードでの関数宣言も `var` と同様に、もっとも近い関数またはグローバルスコープの先頭に巻き上げられる。

```js
hello(); // => 'Hello'
function hello() {
    return 'Hello';
}
```

のようなコードは、

```js
function hello() {
    return 'Hello';
}
hello(); // => Hello
```

のように解釈されたと考えることができる。
`function` で宣言された関数の巻き上げは `var` によって宣言された変数と違って関数が `undefiend` になることがなく呼び出しはできるため、実質的な問題となることは少ない。

無名関数を `var` に代入した場合は、上記の `var` の挙動に則るため、巻き上げにより `undefined` となって関数を呼び出すことができない。

```js
hello(); // => Uncaught TypeError: hello is not a function
var hello = function() {
    return 'hello';
}
```

### 即時実行関数

グローバルスコープの汚染を防ぐために即時実行関数 (IIFE, Immediately-Invoked Function Expression) と呼ばれる方法がある。
関数宣言を `()` で囲み、関数式として解釈さえてから最後の `()` で関数を即時的に実行させている。

```js
(function () {
    var x = 'foo';
    console.log(x); // => 'foo'
})();
// x はグローバルスコープから参照できない
console.log(typeof foo === 'undeifned'); // => true
```

ES2015 以降では`let` と `const` でブロックスコープ内で変数宣言できるため、グローバルスコープの汚染を防ぐための即時実行関数は必要なくなった。

```js
{
    const x = 'foo';
    console.log(x); // => 'foo'
}
console.log(typeof foo = 'undeifned'); // => true
```

### クロージャー

外側のスコープの変数への参照を保持できる関数がもつ性質。下記のように関数ごとに `count` という変数を保持している。

```js
function createCounter() {
    let count = 0;
    function increment() {
        count =+ 1;
        return count;
    }
    return increment;
}

const conunter = createCounter();
conunter(); // => 1
conunter(); // => 2
const anotherCounter = createCounter();
anotherCounter(); // => 1
anotherCounter(); // => 2
anotherCounter(); // => 3
```

#### 静的スコープ

JavaScript ではどの識別子がどの変数を参照するかが静的に決定できる性質がある。(静的スコープ)
下記のように、実行される箇所に関係なく変数の参照先が決定でき、これは `function` で宣言された関数、アロー関数、メソッドで共通の性質である。

```js
const x = 10;
function print() {
    console.log(x); // => 10
}
function run() {
    const x = 20;
    print(); // => 10
}
```

#### 動的スコープ

関数呼び出し時に変数の参照先が決定し、呼び出される場所によって参照先が変わる場合のスコープを動的スコープという。
JavaScript では `this` という特別なキーワードだけは呼び出し元によって参照先が変わることがある。

### メモリ管理の仕組み

- プログラミング言語では一般的に使わない変数やデータを解放する仕組みをもっている
    - ハードウェアのメモリは有限なため、メモリからデータが溢れないようにメモリからデータを適宜解放する必要がある
- JavaScript ではガーベッジコレクション (GC) が不要なデータを解放する仕組みを採用している
    - どこからも参照されなくなったデータを ＧＣ が適宜解放している

```js
let x = 'before';
x = 'after';
// 'before' はどこからも参照されないため、GC によってメモリから解放される
```

```js
function createArray() {
    const tmp = [1, 2, 3];
    return tmp;
}
const arr = createArray();
console.log(arr); // => [1, 2, 3]
// 変数 arr が [1, 2, 3] を参照していて解放されない
```

### クロージャーがなぜ動くのか

クロージャーは静的スコープと、参照され続ける変数のデータが保持されるという性質によって成立している。

### クロージャーの用途

- 関数に状態を持たせる手段
- 外から参照できない変数を定義する手段
- グローバル変数を減らす手段
- 高階関数の一部分

### 状態を持つ関数オブジェクト

JavaScript では関数はオブジェクトの一種のため、プロパティを直接代入できるが、推奨されない。クロージャーで関数内のみで参照できる状態を扱えるようにする。

```js
function countUP() {
    countUp.count += 1;
    return countUp.count;
}
countUp.count = 0;
console.log(countUp()); // => 1
console.log(countUp()); // => 2
```

## 1.18 関数と this

`this`  の参照先は主に次の条件によって変化する。

- 実行コンテクストにおける `this`
- コンストラクタにおける `this`
- 関数とメソッドにおける `this`
- アロー関数における `this`

`this` が実際に使われるのはメソッドの内部が主なので、そこでの振る舞いを覚えておけばよい。

### 実行コンテキストと `this`

#### スクリプトモードにおける `this`

```html
<script>
console.log(this); // => window
</script>
```

#### モジュールモードにおける `this`

```html
<script type="module">
console.log(this); // => undeinfed
</script>
```

ES2020 では実行環境のグローバルオブジェクトを `globalThis` を用いて参照することができる。

### 関数とメソッドにおける `this`

#### アロー関数以外の関数における `this`

- `this` は実行時に決定される
- 参照先はベースオブジェクト (そのメソッド・関数が所属しているオブジェクト) なり、ベースオブジェクトが存在しない場合は `this` は `undefined` になる

#### 関数宣言や関数式における `this`

`'use strict';` がある場合 (strict mode) の場合は以下では `undefined` になり、そうでない場合はグローバルオブジェクトを参照するよう変換されてしまう。

```js
'use strict';
function fn1() {
    return this;
}
const fn2 = function() {
    return this;
}
console.log(fn1()); // => undefined
console.log(fn2()); // => undefined

function fn3() {
    function inner() {
        console.log(this);
    }
}
fn3(); // => undefined
```

#### メソッド呼び出しにおける `this`

```js
const obj = {
    method1: function() {
        return this;
    },
    method2() {
        return this;
    },
};
console.log(obj.method1()); // => obj
console.log(obj.method2()); // => obj

const obj2 = {
    inner1: {
        inner2: {
            method() {
                return this;
            },
        },
    },
};
// method が属している `inner2` を参照
console.log(obj2.inner1.inner2.method() === obj2.inner1.inner2); // => true
```

### `this` が問題になるパターン

#### `this` を含むメソッドを変数に代入したい場合

あるオブジェクト内のメソッドを別の変数に代入して関数として実行すると、ベースオブジェクトが変化する。

```js
'use strict';

const person = {
    name: 'foo',
    sayName: function() {
        return this.name;
    },
};
console.log(person.sayName()); // => 'foo'
const { sayName } = person;
// this は undeifned なので例外を投げる
console.log(sayName()); // => Uncaught TypeError: Cannot read property 'name' of undefined
```

##### 対策 (call, apply, bind メソッド) 

- 関数オブジェクトには `call`, `apply`, `bind` のように明示的に `this` を指定して関数を呼び出せるメソッドが存在する。

- `Functio#call(this の値, ...引数)

    - `this` を指定する必要のない場合は `null` を渡す

    ```js
    'use strict';
    const person = {
        name: 'foo',
        say: function(msg) {
            return msg + this.name;
        },
    };
    const { say } = person;
    console.log(say.call(person, 'Hello, ')); // => 'Hello, foo'
    ```

- `Functio#apply(this の値, [...引数])`

    - `this` を指定する必要のない場合は `null` を渡す

    ```js
    'use strict';
    const person = {
        name: 'foo',
        say: function() {
            return this.name;
        },
    };
    const { say } = person;
    console.log(say.apply(person, ['Hello,'])); // => 'Hello, foo'
    ```

- `Functio#bind(this の値, ...引数)`

    ```js
    'use strict';
    const person = {
        name: 'foo',
        say: function() {
            return this.name;
        },
    };
    const { say } = person;
    console.log(say.bind(person, 'Hello,')()); // => 'Hello, foo'
    ```

    #### 問題: コールバック関数と this

    コールバック関数内で`this` を呼ぶと問題が生じる場合がある。

    ```js
    const obj = {
        prefix: 'pre',
        prefixArray(strs) {
            return strs.map(function(str) {
                return this.prefix + '-' + str;
            });
        },
    };
    obj.prefixArray([1, 2, 3]); // => ["undefined-1", "undefined-2", "undefined-3"]
    ```

    ##### 対策: this を一時変数に代入する

    ```js
    'use strict';
    const obj = {
        prefix: 'pre',
        prefixArray(strs) {
            const self = this;
            return strs.map(function(str) {
                return self.prefix + '-' + str;
            });
        },
    };
    obj.prefixArray([1, 2, 3]); // => ["pre-1", "pre-2", "pre-3"]
    ```

    ##### 対策: アロー関数でコールバック関数を扱う

    アロー関数では暗黙的な `this` の値を受けず、スコープチェーンの仕組みと同様に外側の関数を探索して `this` を参照する。

    ```js
    'use strict';
    const obj = {
        prefix: 'pre',
        prefixArray(strs) {
            return strs.map((str) => self.prefix + '-' + str);
        },
    };
    obj.prefixArray([1, 2, 3]); // => ["pre-1", "pre-2", "pre-3"]
    ```

### アロー関数と `this`

- アロー関数では定義された関数やメソッドにおける `this` が関数定義時に静的に決定する
- アロー関数内では `this` を暗黙的な引数として受け付けず、外側の関数を順に探索していく
- `this` は ECMAScript 上のの予約語のため、ユーザーが勝手に定義できない



### アロー関数は `this` を bind できない

アロー関数で遅疑した関数は `call` や `apply`、`bind` を使った `this` の指定はできない。 (無視される)

```js
const fn = () => {
    return this;
};
console.log(fn()); // グローバルオブジェクト
console.log(fn.call({})); // グローバルオブジェクト
```



## 1.19 クラス

- 構造、動作、状態を定義できるもの
- インスタンスというオブジェクトを作成できる
- 定義された動作を継承し、動作によって状態も変化する

### クラス定義

`function` キーワードによる関数宣言に似ているところがある。

```js
// クラス定義
class MyClass {
    constructor() {}
}
// 変数に代入
const MyClass = class MyClass {
    constructor() {}
};
// クラス名を省略できる
const MyClass = class {
    constructor() {}
}
// constructor がない場合は省略できる
class MyClass {}
```

### クラスのインスタンス化

```js
class MyClass {}
const myClass1 = new MyClass();
const myClass2 = new MyClass();
// 別物
console.log(myClass1 === myClass2); // => false
// instanceof 演算子であるクラスのインスタンスかを調べることができる
console.log(myClass1 instanceof MyClass); // => false
```

- `new` 演算子とともに関数呼び出しのようにして引数を渡すと、constructor メソッドの仮引数に渡される。
- コンストラクタは初期化処理を行う場所なので、`return` は書くべきでない
    - return しない場合はインスタンスのオブジェクトが返される
- クラス名は慣習的に大文字で始まる

```js
class Point {
    constructor(x, y) {
        // プロパティに設定
        this.x = x;
        this.y = y;
    }
}

const point = new Point(3, 5);
console.log(point.x); // => 3
console.log(point.y); // => 5
```

### `class` 構文と関数でのクラスの違い

- `class` 構文では関数呼び出しはできない (誤用を防ぐ)

```js
const Point = function PointConstructor(x, y) {
    this.x = x;
    this.y = y;
};
const point = new Point(3, 4);
// 関数呼び出しもできる
const point2 = Point(3, 4);
```

```js
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
}
const point = new Point(3, 4);
// 関数呼び出しはできない (例外を投げる)
const point2 = Point(3, 4); // Uncaught TypeError: Class constructor MyClass cannot be invoked without 'new'
```

### クラスのプロトタイプメソッドの定義

メソッドでクラスから作られたインスタンスの動作を定義する

```js
class MyClass {
    constructor() {
        this.count = 0;
    }
    increment() {
        this.count++;
    }
}
const counterA = new Counter();
const counterB = new Counter();
counterA.increment();
console.log(counterA.count); // => 1
// プロパティはイスタンスごとに独立
console.log(counterB.count); // => 0
// メソッドはインスタンス間で共有されている
console.log(counterA.increment === counterB.increment); // => true
```

### クラスのインスタンスに対してメソッドを定義する

`this` からメソッドを定義すると、インスタンスオブジェクトにメソッドを定義できる。この場合はインスタンス間でメソッドが共有されていない。

```js
class Counter {
    constructor() {
        this.count = 0;
        this.increment = () => {
            this.count++;
        };
    }
}

const counterA = new Counter();
const coutnerB = new Coutnre();
counterA.increment();
console.log(counterA.count); // => 1
// プロパティはインスタンスごとに独立
console.log(counterB.count); // => 0
// メソッドもインスタンス間で独立
console.log(counterA.increment === counterB.increment); // => false
```

メソッドをインスタンスからアロー関数で指定した場合は、`this` への参照が固定されるが、メソッドでは固定されない。

```js
class Counter {
    constructor() {
        this.count = 0;
    }
    increment() {
        this.count++;
    }
}

const counterA = new Counter();
const { increment } = counterA;
increment(); // => Uncaught TypeError: Cannot read property 'count' of undefined
```

### クラスのアクセッサプロパティの定義

- プロパティの参照時 (getter) とプロパティの代入時 (setter) に対する特別なメソッドを定義できる (アクセッサプロパティ)
- メソッドの前に `get` / `set` を付与するとアクセッサプロパティとして定義できる
- getter (`get`) は仮引数を持たず、値を返す
- setter (`set`) は仮引数を持ち、値を返さない

```js
class Counter {
    constructor(val = 0) {
        this._count = val;
    }
    get count() {
        return this._count;
    }
    set count(c) {
        this._count = c;
    }
}
const counter = new Counter();
console.log(counter.count); // 0
counter.count = 3;
console.log(counter.count); // 3
```

### プライベートプロパティ

- 前述の項ではプロパティ名の先頭に `_` をつけて読み書きしてほしくない旨を示したが、実際には読み書きできてしまう
- 現在 `#` をつけたプロパティをプライベートプロパティとする[プロポーザル](https://github.com/tc39/proposal-private-methods)が存在する
- WeakSet で同等の仕組みを実現することができる

### 静的メソッド

`static `で宣言されたメソッドはクラスをインスタンス化せずに利用できる静的メソッド (クラスメソッド)  がある。静的メソッド内では `this` はインスタンスではなくクラス自身を指す。

```js
class Counter {
    static method() {
        console.log('static');
    }
}
// 静的メソッドの呼び出し
Counter.method(); // => 'static'
```

### 2種類のインスタンスメソッドの定義

- プロトタイプメソッドとインスタンスオブジェクトのメソッドは両方とも定義されている
- インスタンスオブジェクトが優先して呼ばれる

### プロトタイプオブジェクト

- プロトタイプメソッドはプロトタイプオブジェクトへ、インスタンスオブジェクトのメソッドはインスタンスオブジェクトへ定義される
- プロトタイプオブジェクト: 関数オブジェクトの `prototype` プロパティに自動的に生成される特殊なオブジェクト

```js
class MyClass {
    method() {}
}
// prototype プロパティにプロトタイプオブジェクトが存在
console.log(typeof MyClass.prototype === 'object'); // => true
const myClass = new MyClass();
// constructor はクラス自身を参照
console.log(MyClass.prototype.constructor === MyClass); // => true
console.log(MyClass.prototype.method === myClass.method); // => true
```

### プロトタイプチェーン

- インスタンス作成時にクラスのプロトタイプへの参照が `[[Prototype]]` という内部的なプロパティに保持される (便宜的に `[[Prototype]]` と表現している)
- `Object.getPrototypeOf()` メソッドで `[[Prototype]]` 内部プロパティを参照できる
- `Object.setPrototypeOf()` メソッドで `[[Prototype]]` 内部プロパティを設定できる
- `[[Prototype]]` 内部プロパティを通常のプロパティのように扱える  `__proto__` というアクセッサプロパティが存在するが通常は触らない

### プロパティの参照とプロトタイプチェーン

オブジェクトのプロパティにアクセスされたとき、

1. `instance` オブジェクト自身
2. `instance` オブジェクトの `[[Prototype]]` の参照先
3. どこにもない場合は `undefined`

の順で探索している。

```js
class MyClass {
    method() {
        console.log('method');
    }
}
const instance = new MyClass();
const Protptype = Object.getPrototypeOf(instance);
console.log(instance.method === Prototype.method); // => true
```

class 構文を使う際はプロトタイプオブジェクトやプロトタイプチェーンの仕組みを意識する必要はないが、表現として存在していることは知っておくとよい。

### 継承

- `extends` で既存のクラス定義を継承して新しいクラスを定義できる
- `super` というキーワードで親クラスを参照できる

```js
class Parent {
    constructor(...args) {
        console.log('parent', ...args);
    }
}
class Child extends Parent {
    constructor(...args) {
        super(...args);
        console.log('child', ...args)
    }
}
const child = new Child('arg1', 'arg2');
// parent arg1 arg2
// child arg1 arg2
```

また、Child の `constructor` メソッド (コンストラクタ) を省略した場合、以下と同じである。

```js
class Parent {}
class Child extends Parent {
    constructor(...args) {
        super(...args);
    }
}
```

### コンストラクタの処理順

親クラス→子クラスへとコンストラクタが処理される。

### プロトタイプ継承

子 → 親の順で探索される。

```js
class Parent {
    parentMedhod() {
        console.log('parent method');
    }
}
class Child extends Parent {}
const instance = new Child();
instance.method(); // "parent method"
```

### 静的メソッドの継承

クラスのコンストラクタ内にも `[[Prototype]]` 内部プロパティがあり、子 → 親へと探索していく。

```js
class Parent {
    static hello() {
        return 'hello';
    }
}
class Child extends Parent {}
console.log(child.hello()); // => 'hello'
```

### `super` プロパティ

```js
class Parent {
    method() {
        console.log('parent method')
    }
}
class Child extends Parent {
    method() {
        console.log('child method');
        super.method();
    }
}
const child = new Child();
child.method();
// => parent method
// => child method
```

### 継承の判定

```js
class Parent {}
class Child extends Parent {}
const parent = new Parent();
const child = new Child();
console.log(parent instanceof Parent); // => true
console.log(parent instanceof Child); // => false
console.log(child instanceof Parent); // => true
console.log(child instanceof Child); // => true
```

### ビルトインオブジェクトの継承

既存のオブジェクトを継承することもできる。

```js
class MyArray extends Array {
    get first() {
        if (this.length === 0) {
             return undefined;   
        } else {
            return this[0];
        }
    }
}
const array = MyArray.from([1, 2, 3, 4, 5]);
console.log(array.length); // => 5
```



# 1.20 例外処理

## `try...catch` 構文

- `try`...catch 構文内の `try` ブロック内で例外が発生すると、`try` ブロック内の以降の分は実行されず、`catch` 節に移行する
    - `catch` 節では投げられた例外のオブジェクト (例外識別子) を参照できるオブジェクトに関数の引数のように呼び出せる
- `finally` 節は `try` での例外の有無に関係なく必ず最後に実行される

### throw 文

ユーザーが例外を投げることができる

```js
try {
    throw new Error('Error');
} catch(err) {
    console.error(err.message); // => 'Error'
}
```

### エラーオブジェクト

#### `Error`

- `Error` オブジェクトをコンストラクタから呼び出すことで作成できる
- 第一引数にはエラーメッセージを渡し、`Error#message` からアクセスできる
- `throw` 文ではあらゆるオブジェクトを投げることができるが、原則的には `Error` オブジェクトを投げる
    - `Error` オブジェクトはインスタンス作成時にファイル名、行数などの情報 (スタックトレース) を持っていてデバッグに役に立つため有用

```js
try {
    throw 'Error';
} catch(err) {
    console.error(err); // => 'Error'
}
```

### ビルトインエラー

エラーの状況に合わせたビルトインオブジェクトがビルトインエラーが予め定義されている。

- Reference Error
    - 存在しない変数や関数などの識別子が参照されたときのエラー
- Syntax Error
    - 構文的に正しくないコードを解釈しようとした場合のエラー
    - JavaScript コードを実行する前にパースする段階で発生する
- TypeError
    - 値が期待された型と相違がある場合のエラー

### ビルトインエラーを投げる

`Error` オブジェクトではなく、ビルトインオブジェクトのインスタンスを例外として投げることもできる

```js
const reverseString = (str) => {
    if (typeof str !== 'string') {
        throw new TypeError(`${str} is not a string`);
    }
    return Array.from(str).reverse().join();
};

try {
    reverse(100);
} catch(err) {
    console.log(err instanceof TypeError); // => true
    console.log(err.name); // => 'TypeError'
    console.log(err.message); // => '100 is not a string'
}
```

### エラーとデバッグ

- デバッグ中に発生したエラーの情報からどこでどのような例外が投げられたのかを知ることができる
- スタックトレースでは上から下へ呼び出し元をたどれるように記録される

### `console.error` とスタックトレース

`console.log` ではメッセージのみが表示されるが、`console.error` ではメッセージとともにスタックトレースが標準出力として出力される。



### 1.21 非同期処理 : コールバック/Promise/Async Function

### 同期処理

- コードを上から順番に実行していく
- ひとつの処理が終了するまでは次の処理は行われない
- JavaScript では1つのメインスレッド (UI スレッド) で実行されると、同期的にブロックする処理があると他の処理で占有されてしまい表示の更新が遅れたりしうる

```js
const blockTime = (timeout) => {
    const startTime = Date.now();
    while (true) {
        const diff = Date.now() - startTime;
        if (diff >= timeout) return;
    }
};

console.log('start');
blockTime(1000);
console.log('end (約1秒後に実行される)');
```

### 非同期処理

- コードを上から順番に実行していく
- **ひとつの非同期処理の終了を待たずに次の処理を評価する**

```js
const blockTime = (timeout) => {
    const startTime = Date.now();
    while (true) {
        const diff = Date.now() - startTime;
        if (diff >= startTime); return;
    }
};

console.log('1. start');
// 約10ms後に実行される
setTimeout(() => {
    console.log('3. start asynchronous');
    blockTime(1000);
    console.log('4. end block')
}, 10);
console.log('2. synchronous');
```

### 非同期処理はメインスレッドで実行される

- JavaScript において多くの非同期処理はメインスレッド (UI スレッド) で実行される
    - ECMAScript で規定されているためではないため、すべてがメインスレッド内で実行されているわけではない
- メインスレッドで実行される非同期処理は、メインスレッドをブロックする同期処理の影響を受ける
- 非同期処理は並行処理 (concurrent) として扱われ、一定の単位ごとに処理が切り替えられながら実行される
    - 実行中に重たい処理があると非同期処理の切り替えが遅れる
- Web ブラウザでは別のスレッドで実行 (並列処理) できる API として Web Worker API が存在する
    - メインスレッドとのデータのやりとりは `postMessage` というメソッドを利用する必要がある

### 非同期処理と例外処理

- `try...catch` 構文では同期的に発生した例外しかキャッチできない
    - 非同期処理内で `try...catch` 構文を使えば内部で例外の発生を検知することはできる
- 非同期処理の外部に非同期処理内の例外の発生を伝えるにはいくつかの方法がある

#### エラｰファーストコールバック

- Promise が2015年に仕様に入るまではエラーファーストコールバックという非同期中の例外をコールバック関数内で扱うルールが 使われた
    - 単なるルールであって仕様として標準化されているわけではない
- 処理が失敗した場合はコールバック関数の第一引数にエラーオブジェクトを渡す
- 処理が成功した場合はコールバック関数の第一引数に `null` を渡し、2番目以降の引数に成功時の結果を渡す

```js
fs.readFile('./example.txt', (err, data) => {
    if (err != null) {
        // データ読み込み失敗
    } else {
        // データ読み込み成功
    }
});
```

また、成功したときと失敗したとき用の関数の2つを受け取って処理をする方法もある。

```js
const dummyFetch = (path, successCallback, failureCallback) => {
    setTimeout(() => {
        if (path.startsWith('/success')) {
            successCallback({ body: `Reponse body of ${path}` });
        } else {
            failureCallback(new Error('NOT FOUND'));
        }
    }, 1000 * Math.random());
};
```

### Promise (ES2015 以降)

- 非同期処理の結果を表現するためのビルトインオブジェクト
- 非同期処理が成功したときの処理がコールバック関数として `then` メソッドへ渡され、失敗したときの処理が `catch` メソッドへ渡される
- `Promise` インスタンスのメソッドによって渡せる引数が決まっているため、非同期処理の流れを統一的に表現することができる

```js
asyncTask((err, result) => {
    if (err != null) {
        // 非同期処理が失敗したときの処理
    } else {
        // 非同期処理が成功したときの処理
    }
});
```

### `Promise` インスタンスの作成

```js
const executor = (resolve, reject) => {
    // 非同期の処理が成功したときは resolve を呼ぶ
    // 非同期の処理が失敗したときは reject を呼ぶ
};
const promise = new Promise(executor);
const onFullfilled = () => { console.log('resolved'); };
const onRejected = () => { console.log('rejected'); };
// `then` メソッドで成功時と失敗時に呼ばれるコールバック関数を登録
promise.then(onFullfilled, onRejected);
// `catch` メソッドは `then` の第二引数にコールバックを書くのと同じ
promise.then(onFullfilled).catch(onRejected);
```

### `Promise#then` と `Promise#catch`

失敗時のコールバック関数のみ登録する場合は `catch` メソッドの利用が推奨される

```js
const delay = (timeout) => {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve();
        }, timeoutMs);
    });
};

const fullfilledCallback = () => { console.log('resolved'); };
const rejectedCallback = () => { console.log('rejected'); };
// 失敗時のコールバックを省略
delay.then(fullfilledCallback);
// 成功時のコールバックを省略
delay.then(undefined, rejectedCallback);
// 第一引数に成功時のコールバック、第二引数に失敗時のコールバックを登録
delay.then(fullfilledCallback, rejectedCallback);
// then メソッドに成功時のコールバック、catch メソッドに失敗時のコールバックを登録
delay.then(fullfilledCallback).catch(rejectedCallback);
```

### Promise と例外

非同期処理内で発生した例外は `then` メソッドの第二引数や `catch` メソッドの第一引数に登録されたコールバック関数内で処理される。

```js
const throwPromise = () => {
    return new Promise((resolve, reject) => {
        throw new Error('Exception');
    });
};

throwPromise().catch((err) => {
  console.log(error.message); // => 'Exception'
});
```

### `Promise` の状態

`Promise` インスタンスには内部的に以下の3つの状態が存在する。

- Fullfilled
    - `resolve` したときの状態
    - `then` メソッドの第一引数に渡されたコールバック関数が呼ばれる
- Rejected
    - `reject` または例外が発生したときの状態
    - `then` メソッドの第二引数に渡されたコールバック関数か、もしくは `catch` 関数に渡されたコールバック関数に渡される
- Pending
    - Fullfilled または Rejected でない状態
    - `new Promise` でインスタンスを作成したときの初期状態
    - 一度 Fullfilled または Rejected の状態 (Settled) になればそれ以降は変化しない

```js
const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(); // この時点で Fullfilled からは変わらない
        reject(new Error('Error'));
    });
}, 10);
promise.then(() => {
    console.log('fullfilled');
}, (err) => {
    // 呼ばれない
});
```

### `Promise.resolve`

Fullfilled になった `Promise` インスタンスを作成できる

```js
const fullfilled = Promise.resolve(1);
// 以下と同じ意味
const fullfilled2 = new Promise((resolve) => { resolve(1); });

fullfilled.then((i) => {
    console.log(`callback is called with ${i}`); // => callback is called with 1
});
```

### `Promise.reject`

Rejected になった `Promise` インスタンスを作成できる

```js
const rejected = Promise.reject(new Error('Error'));
// 以下と同じ意味
const rejected2 = new Promise((_, reject) => { reject(new Error('Error')); });

rejected.catch((err) => {
    console.log(`callback is called with Error, ${err.message}`);
});
```

`Promise.resolve` や `Promise.reject` は短く書くことができるため、テストなどで利用されることがある。

### Promise チェーン

- 複数の非同期処理をメソッドチェーンでつないで、順番に扱うことができる (Promise チェーン)
- Rejected になった場合は最も近い失敗時の処理が呼び出され、それまでの成功時の処理はスキップされる
- Rejected になった Promise を `catch` メソッドなどで捕捉すると、次には成功時の処理が呼ばれる

### Promise チェーンで値を返す

Promise チェーン内で、コールバックで返した値は次のコールバックの引数に渡される。

```js
Promise.resolve(1).then((value) => {
    console.log(value); // => 1
    return value * 3;
}).then((value) => {
    console.log(value); // => 3
    return value ** value;
}).then((value) => {
    console.log(value); // => 9
}).then((value) => {
    console.log(value); // => undefined
});
```

### コールバック関数で `Promise` インスタンスを返す

コールバック関数内で `Promise` インスタンスを返したとき、Rejected な状態の Promise インスタンスを返した場合は次に失敗時の処理が呼ばれる。

```js
Promies.resolve().then(() => {
    return Promise.reject(new Error('Error'));
}).then(() => {
    console.log('呼ばれない');
}).catch((err) => {
    console.error(err.message); // => 'Error'
}).then(() => {
    console.log('呼ばれる');
});
```

### Promise チェーンの最後に処理を書く

`finally` で非同期処理の終了状態 (Fullfilled / Rejected) に関わらず、`Promise#finally` メソッドを使うことで代入を1カ所にまとめることができる

```js
const promise = Math.random() < 0.5 ? Promise.resolve() : Promise.reject();
promise.then(() => {
    console.log('Promise#then');
}).catch((err) => {
    console.log('Promise#catch');
}).finally(() => {
    console.log('Promise#finally');
});
```

### `Promise.all` で複数の Promise をまとめる

- `Promise.all` で Promise インスタンスを配列で渡すと、配列内の Promise インスタンスがすべて Settled 状態になるまで待機する
- 一つでも失敗した場合は `catch` のコールバック関数が呼ばれる

```js
const delay = (timeoutMs) => {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve(timeoutMs);
        }, timeoutMs);
    });
};
Promise.all([
    delay(1),
    delay(2),
    delay(3),
]).then((values) => {
    console.log(values); // => [1, 2, 3]
});
```

### `Promise.race`

- `Promise.all` と同様に Promise インスタンスを配列で複数渡す
- 配列内の Promise インスタンスのうち最初に Settled 状態になれば次の処理を実行
- 最初に Settled になった Promise が Fullfilled の場合は Fullfilled を、Rejected の場合は Rejected を返す

```js
const delay = (timeoutMs) => {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve(timeoutMs);
        }, timeoutMs);
    });
};
Promise.all([
    delay(10),
    delay(20),
    delay(30),
]).then((value) => {
    console.log(value); // => 10
});
```

### Async Function (ES2017 以降)

- ES2017 では Async Function という非同期処理を行う関数を定義する構文が導入された
- Async Function では必ず Promise インスタンスを返す

```js
async function doAsync() {
    return 1;
};
doAsync.then((value) => {
    console.log(value); // => 1
});

// 下記と同じ意味
function doAsync2() {
    return Promise.resolve(2);
}
doAsync2().then((value) => {
    console.log(value); // => 2
})
```

### Async Function の定義

関数宣言、関数式、アロー関数、メソッドなどどの定義方法でも `async` キーワードを前につけるだけで Async Function として定義できる

```js
async function fn1() {}
const fn2 = async function() {};
const fn3 = async () => {};
const obj = { async method() {} };
```

### Async Function は Promise を返す

- Async Function が値を return した場合、その返り値を持つ Fulfilled な Promise を返す
- Async Function がPromiseを return した場合、その返り値の Promise をそのまま返す
- Async Function 内で例外が発生した場合は、そのエラーを持つ Rejected な Promise を返す

### `await ` 式

- Async Function の関数内では `await ` が利用できる
- 右辺の Promise インスタンスが Settled 状態になるまで待機し、Promise インスタンスの状態が変わると次の処理の実行を再開する
- 同期処理の感覚でコールバック関数を使用せずに非同期処理を記述できる

```js
async function asyncMain() {
    // await 式で評価した右辺の Promise が Rejected となったため、例外がthrowされる
    const value = await Promise.reject(new Error('Error'));
    // await式で例外が発生したため、この行は実行されません
}
// Async Functionは自動的に例外をキャッチできる
asyncMain().catch(error => {
    console.log(error.message); // => 'Error'
});
```

### Promise チェーンを `await` 式で表現する

```js
// プロミスチェーン
function fetch() {
    const results = [];
    return fetch('/resources/1')
        .then((res) => {
            results.push(res.body);
        })
        .then((res) => {
            results.push(res.body);
        });
};

// await
function fetch2() {
    const results = [];
    const res1 = await fetch('/resources/1');
    results.push(res1.body);
    const res2 = await fetch('/resources/2');
    results.push(res2.body);
    return results;
}
```



### Async Function と組み合わせ

#### Async Function と反復処理

`for` 文などの中で `await` することで反復処理中に非同期処理を待機することができる

```js
async function fetchResources(resources) {
    const results = [];
    for (const resource of resources) {
        const res = await fetch(resource);
        results.push(res.body);
    }
    return results;
}

const resources = [
    '/resources/1',
    '/resources/2',
];
fetchResources(resources).then((results) => {
    console.log(results);
});
```

#### Promise API と Async Function を組み合わせる

前述の `Promise.all` メソッドと `await` 式を組み合わせることもできる。

```js
async function fetchResources(resources) {
    const promises = resources.map((resource) => fetch(resource));
    const responses = await Promise.all(promises);
    return responses.map((res) => res.body);
}

const resources = [
    '/resources/1',
    '/resources/2',
];
fetchResources(resources).then((results) => {
    console.log(results);
});
```

### `await` 式は Async Function の中でのみ利用可能

```js
async function asyncMain() {
    await new Promise((resolve) => {
        setTimeout(resolve, 16);
    });
}

console.log('1. asyncMain を呼び出す');
asyncMain().then(() => {
    console.log('3. asyncMain が完了する');
});
// Async Function 外部の処理が止まるわけではない
console.log('2. 同期的処理が続行');
```

コールバック関数内で `await` 式を使う場合は、コールバック関数に `async` をつける必要がある。

#### try...catch と await...catch

```js
// try...catch
async function fetch1() {
    try {
        const res = await fetch('/resources/1');
        return res;
    } catch(err) {
        console.error(err);
        return undefined
    }
}

// await...catch
async function fetch2() {
    const res = fetch('/resources/1').catch((err) => {
        console.error(err);
        return undefined;
    });
    return res?.body;
}
```

## 1.23 Map / Set

### Map

- マップ型のコレクションを扱うビルトインオブジェクト
- キーと値からなる
- 辞書、ハッシュマップ、連想配列などとも

### マップの作成と初期化

```js
const map1 = new Map();
console.log(map.size); // => 0

const map2 = new Map([['key1', 'value1'], ['key2', 'value2']]);
console.log(map.size); // => 2
```

### 要素の追加と取り出し

```js
const map = new Map();
// 新しい要素の追加
map.set('key', 'value');
console.log(map.size);
console.log(map.get('key')); // => 'value1'
// 要素を上書き
map.set('key', 'value2');
console.log(map.get('key')); // => 'value2'
// 存在確認
console.log(map.has('key')); // => true
console.log(map.has('differentKey')); // => false
// 値を削除
console.log(map.delete('key')); // => true
console.log(map.has('key')); // => false
console.log(map.delete('differentKey')); // => false
```

### マップの反復処理

マップが持つ要素を列挙するメソッドとして `forEach`, `keys`, `values`, `entries` がある。

#### `forEach` メソッド

挿入順に反復される。

```js
const map = new Map([['key1', 'value1'], ['key1', 'value2']]);
map.forEach((v, k) => {
   console.log(`${key}: ${value}`);
});
// 'key1: value1'
// 'key2: value2'
```

#### `keys` / `values` メソッド

キー/ 値を挿入順に並べた Iterator オブジェクトを返す。

```js
const map = new Map([['key1', 'value1'], ['key2'], ['value2']]);
for (const k of map.keys()) {
    console.log(k);
}
// 'key1'
// 'key2'
```

### `entries` メソッド

すべての要素 (キーと値) をエントリーとして挿入順に並べた Iterator オブジェクトを返す。

```js
const map = new Map([['key1', 'value1'], ['key2'], ['value2']]);
for (const [k, v] of map.entries()) {
    console.log(`${k}: ${v}`);
}
// 'key1: value1'
// 'key2: value2'
```

### `for...of` による反復処理

マップ自身も iterable なオブジェクトなので、`for...of` 文で反復処理できる。

```js
const map = new Map([['key1', 'value1'], ['key2', 'value2']]);
for (const [k, v] of map) {
    console.log(`${k}: ${v}`);
}
// 'key1: value1'
// 'key2: value2'
```

### マップとしての Object と Map

ES2015 で `Map` が導入されるまで JavaScript では `Object` が利用されてきたが、 `Object` には以下のような問題点もある。

- `Object` の `prototype` オブジェクトから継承されたプロパティによって意図しないマッピングが生じる危険性
- キーは文字列か `Symbol` に限定される

```js
const obj = {};
console.log(typeof obj.someKey); // => 'undefined'
// constructor はプロトタイプオブジェクトに存在
console.log(typeof obj.constructor); // => 'function'
```

`Object.create(null)` で初期化することで回避することもできるが、ES2015 ではこの問題を根本的に解決するため、`Map` を導入した。`Map` には以下のような利点がある。

- `Map` のプロトタイプが持つメソッドやプロパティとキーが衝突しない
- キーにあらゆるオブジェクトを使用することができる
    - オブジェクトをキーにすると参照ごとに違うマッピングができる
- サイズが簡単に得られる
- 要素を簡単に列挙できる

ただ、`Object` をマップとして使う場合にも次の利点がある。

- リテラル表現があるため作成しやすい
- `JSON.stringify` 関数で JSON に簡単に変換できる
- ネイティブの API やライブラリの多くの関数がマップとして Object を渡す設計になっている

### WeakMap

- `Map` と同じようにマップを扱うビルトインオブジェクト
- キーを弱い参照 (Weak Reference) でもつ
    - GC によるオブジェクトの解放を妨げない
    - キーで使えるのは参照型のオブジェクトのみ
- iterable でないので、キー / 値の列挙やサイズを調べることはできない
- クラス内にプライベートな値を保持するのに用いることができる
    - `this` を `WeakMap` のキーにして、クラス内部でしか参照できないようにする
    - クラスインスタンス (`this`) が参照できなくなった時点で GC が発生するタイミングで解放される

```js
const listenersMap = new WeakMap();

class EventEmitter {
    addListener(listener) {
        const listeners = listenersMap.get(this) ?? [];
        const newListeners = [...listeners, listener];
        listenersMap.set(this, newListeners);
    }
}

let eventEmitter = new EventEmitter();
eventEmitter.addListener(() => {
    console.log('An event is fired');
});
// listenersMap も解放される
eventEmitter = null;
```

また、キャッシュとしても用いることができる。

```js
const cache = new WeakMap();
function getHeight(ele) {
    if (cache.has(element)) {
        return cache.get(element);
    }
    const height = ele.getBoundingClientRect().height;
    cache.set(element, height);
    return height;
}
```

### キーの等価性と NaN

```js
const map = new Map();
map.set(NaN, 'value');
// NaN は常に自分自身とは等しくない
console.log(NaN === NaN);
// Map のキーとして使用した場合は等価なものとして扱われる
console.log(map.has(NaN)); // => true
console.log(map.get(NaN)); // => 'value'
```

### Set

- 重複する値がないことを保証したコレクション
- 順序を持たず、インデックスでアクセスできない

### セットの作成と初期化

```js
const set = new Set();
console.log(set.size); // => 0

// 'value2' が重複している
const set2 = new Set(['value1', 'value2', 'value2', 'value3']);
console.log(set.size); // => 3
```

### 値の追加と取り出し

```js
const set = new Set();
set.add('value1');
console.log(set.size); // => 1
// 重複する値は追加されない
set.add('value1');
console.log(set.size); // => 1
// 存在確認
console.log(set.has('value1')); // => true
console.log(set.has('differentValue')); // => false
// 削除
console.log(set.delete('value1')); // => true
console.log(set.delete('defferentValue')); // => false
```

### セットの反復処理

- `Map` と同様に、`forEach`, `keys`, `values`, `entries` のように反復用のメソッドが存在する
- `keys` が `values` のエイリアスになっている
- `entries` は `[値, 値]` のような形のエントリーを列挙する
- `Set` オブジェクト自体も iterable なため、 `for...of` で反復処理できる

### `WeakSet`

- 弱い参照で値を持つ
    - 値として参照型のものしか格納できない
- iterable でないので、反復処理はできない
    - 値の追加、削除、存在確認のみ可能

## 1.24 JSON

### JSON とは

- JavaScript Object Notation の略で、JavaScript のオブジェクトリテラルをベースに作られた軽量なデータフォーマット
- [ECMA-404 - Ecma International](https://www.ecma-international.org/publications-and-standards/standards/ecma-404/) として標準化されている
- 人にとっても読み書きしやすく、マシンにとってもパースや生成がしやすい
- オブジェクト、配列、文字列、数値、真偽値が値として使用できる
- 小数点始まり、ゼロ始まりの数値は無効
- キーはダブルクォート `"` で囲む必要がある

### `JSON` オブジェクト

#### JSON 文字列をオブジェクトに変換する

```js
const obj1 = JSON.parse('{ "id": 1, "name": "foo" }');
console.log(obj.id); // => 1
console.log(obj.name); // => 'foo'

// 配列の場合は配列を返す
const obj2 = JSON.parse('[1, 2, 3]');
console.log(obj); // => [1, 2, 3]
```

JSON として不正な文字列が含まれる場合は例外を投げるため、`try...catch` 構文で処理すべき。

```js
function parseJson(str) {
    try {
        const json = JSON.parse(str);
    } catch(err) {
        console.error(err);
    }
}
```

### オブジェクトを JSON 文字列に変換する

```js
const obj = {
    id: 1,
    name: 'foo',
};
console.log(JSON.stringify(obj)); // {"id":1,"name":"foo"}
```

- 第二引数 (replacer 引数)
    - 関数の場合は引数にキーと値が渡され、返り値によって変換の挙動をコントロールできる
    - 配列の場合は、配列に含まれるキーのみが JSON に変換される
- 第三引数 (space 引数)
    - 変換後の JSON 文字列を読みやすくするために挿入できるスペースを指定できる
    - 第二引数が不要の場合は一般的に `null` が渡される

### JSON にシリアライズできないオブジェクト

| シリアライズ前       | シリアライズ後                 |
| -------------------- | ------------------------------ |
| 文字列・数値・真偽値 | それぞれ対応する値             |
| null                 | null                           |
| 配列                 | 配列                           |
| オブジェクト         | オブジェクト                   |
| 関数                 | 変換されない (配列内なら null) |
| undefined            | 変換されない (配列内なら null) |
| Symbol               | 変換されない (配列内なら null) |
| RegExp               | {}                             |
| Map, Set             | {}                             |

また、循環参照がある場合は失敗する

``` js
const obj = { foo: 1 };
obj.self = obj;
try {
    JSON.stringify(obj);
} catch (err) {
    console.error(err); // => Uncaught TypeError: Converting circular structure to JSON
}
```

### `toJSON` メソッドを使ったシリアライズ

オブジェクトが `toJSON` メソッドを持っている場合、 `JSON.stringify` は既定の文字列返還ではなく、`toJSON` メソッドの返り値を用いる。

## 1.25 Date

### Date オブジェクト

- `Date` オブジェクトも JavaScript のビルトインオブジェクトの1つ
- `Date` における時刻は UTC (協定世界時) の1970年1月1日0:00:00 を基準とした相対的なミリ秒

### インスタンスの作成

#### 現在の時刻をインスタンス化

```js
const now = new Date();
console.log(Date.now()); // => 1621922067307
// Date オブジェクトから時刻値を取得
console.log(now.getTime()); // => 1621922067307
// IOS 8601 形式の文字列で表示
console.log(now.toIOSString()); // => '2021-05-25T05:54:27.307Z'
```

### 任意の時刻をインスタンス化

`Date` オブジェクトでは次の3種類を引数としてサポートしている。

- 時刻値  (UTC を基準としたミリ秒)

```js
const date = new Date(1021912067307);
console.log(date); // => 2002-05-20T16:27:47.307Z
```

- 時刻を示す文字列 (RFC2822 や ISO8601 など)

```js
// UTC における時刻
const inUTC = new Date('2006-01-02T15:04:05.999Z');
console.log(inUTC.toISOString()); // => '2006-01-02T15:04:05.999Z'

// ローカルでの時刻 (UTC であることを表す 'Z' がついてない)
const inLocal = new Date('2006-01-02T15:04:05.999');
console.log(inLocal.toISOString()); // => '2006-01-02T06:04:05.999Z' (Asia/Tokyoの場合)
```

- 時刻の部分 (年・月・日など) を数値で表すもの
    - コンストラクタに複数の引数を渡すとこのオーバーロードが適用される
    - 月を表す数値は 0 始まり
    - 渡した数値は常に実行環境のローカルのタイムゾ－ンに依存しているため、基本的にこの方法は使うべきではない
    - 部分ごとに指定したい場合は `Date.UTC` メソッドを用いるとよい

```js
// タイムゾーンを指定することができない
const date1 = new Date(2006, 0 ,2, 15, 4, 5, 999);
console.log(inUTC.toISOString()); // => '2006-01-02T06:04:05.999Z' (Asia/Tokyo の場合)

// UTC に固定できる
const ms = Date.UTC(2006, 0, 2, 15, 5, 5, 999);
const date2 = new Date(ms);
console.log(date2.toISOString()); // => '2006-01-02T15:04:05.999Z'
```

以上の3つの引数にも当てはまらず、不正な値が渡されてパースが失敗した場合は以下のようになる。

```js
const invalid = new Date('');
console.log(invalid.getTime()); // => NaN
console.log(invalid.toString()); // => 'Invalid Date'
console.log(invalid.toISOString()); // => Uncaught RangeError: Invalid time value
```

### Date のインスタンスメソッド

```js
const formatDate = (date) => {
    const yyyy = date.getFullYear();
    // 月は 0 始まりなので 1 を足す必要がある
    const mm = date.getMonth() + 1;
    const dd = date.getDate();
    // date
    return `${String(yyyy)}${String(mm).padStrt(2, '0')}${String(dd).padStrt(2, '0')}`;
}
console.log(formatDate('2006-01-02T15:04:05.999')); // => '2006/01/02'
```

`getTimezoneOffset` メソッドは、実行環境のタイムゾーンの UTC からのオフセット値を分単位で返す。

```js
const now = new Date();
const timezoneoffsetInHours = now.getTimezoneoffset() / 60;
// UTC の現在の時間を計算できる
console.log(`Hours in UTC: ${now.getHours() + timezoneOffsetInHours}`);
```

### 現実のユースケースと Date

ビルトインの `Date` オブジェクトでは物足りない点がいくつかある。[dayjs](https://github.com/iamkun/dayjs)や[js-joda](https://github.com/js-joda/js-joda) のような外部ライブラリを使うとよい。

## 1.26 Math

### Math オブジェクト

- `Math` オブジェクトはビルトインオブジェクトの一種だが、コンストラクタではない
- すべて定数や静的なプロパティ・メソッドとして提供されている

### 乱数を生成する

```js
const getRandom = (min, max) => {
    return Math.random() + (max - min) + min;
};
// 1以上5未満の浮動小数点を返す
console.log(getRandom(1, 5));
```

### 数値の大小を比較する

```js
console.log(Math.max(1, 10)); // => 10
console.log(Math.max(1, 10)); // => 1

const numbers = [1, 2, 3, 4, 5];
// 可変長の引数をとる
console.log(Math.max(...numbers)); // => 5
console.log(Math.min(...numbers)); // => 1
```

### 数値を整数にする

```js
// 床関数
console.log(Math.floor(1.3)); // => 1
console.log(Math.floor(-1.3)); // => -2
// 天井関数
console.log(Math.ceil(1.3)); // => 2
console.log(Math.ceil(-1.3)); // => -1
// 四捨五入
console.log(Math.round(1.3)); // => 1
console.log(Math.round(1.6)); // => 2
console.log(Math.round(-1.3)); // => -1
// trunc 関数 (ES2015 以降)
console.log(Math.trunc(1.3)); // => 1
console.log(Math.trunc(-1.3)); // => -1
```

## 1.27 ECMAScript モジュール

モジュールは保守性、名前空間、再利用性のために使用される。

- 保守性: 依存性の高いコードを1カ所にまとめ、それ以外へのモジュールの依存を減らせる
- 名前空間: モジュールごとにスコープをもち、グローバルの名前空間を汚染しない
- 再利用性: よく使う関数などをモジュールとして再利用できる

### ECMAScirpt モジュールの構文

- パース段階でモジュールの依存関係が解決され、存在しないモジュールを参照している場合はパース段階でエラーになる
- `import` と `export` でモジュールのインポート / エクスポートができ、それぞれに名前付きとデフォルトの2種類がある

#### 名前付きエクスポート / インポート

```js
// named-export.js
const foo1 = 'foo1';
const foo2 = 'foo2';
export { foo1, foo2 };

export function bar () {};
export const baz = 'baz';
```

```js
// as で名前付きエクスポートされたものに別名 (エイリアス) をつけることができる
import { foo1, foo2 as myFoo2, bar } from './named-export.js';
console.log(foo1); // => 'foo1'
console.log(myFoo2); // => 'foo2'
console.log(bar); // => ƒ bar() {}
```

### デフォルトエクスポート / インポート

- デフォルトエクスポートはモジュールごとに1つしかエクスポートできない
- `export default` としてエクスポートする関数・変数などを指定する
- `default` という名前でエクスポートすることで、デフォルトエクスポートの扱いになる

```js
// default-export.js
const foo = 'foo';
export default foo;
```

デフォルトインポートでは、インポートする側が名前をつける。

```js
// default-import.js
import foo from './default-export.js';
console.log(foo); // => 'foo'
```

- 変数宣言とデフォルトエクスポートは同時に行えない
    - 変数宣言はカンマ区切りで複数の変数を宣言できてしまうため
- `default` という名前ではインポートできないので、別名をつける必要がある

```js
import { default as myModule} from './my-module.js';
```

### その他の構文

#### 再エクスポート

```js
// すべての名前付きエクスポートを再エクスポート
export * from './my-module.js';
// すべての名前付きエクスポートを MyNamespace オブジェクトとして再エクスポート
export * as MyNamespace from './my-module.js';
// 名前付きエクスポートしたものを再エクスポート
export { foo, bar } from './my-module.js';
// 名前付きエクスポートしたものを別名で再エクスポート
export { foo as MyModuleFoo, bar as MyModuleBar } from './my-module.js';
// デフォルトエクスポートされているものを再エクスポート
export { default } from './my-module.js';
// デフォルトエクスポートされているものを別名で再エクスポート
export { default as myMoudleDefault } from './my-module.js';
// foo で名前付きエクスポートされているものをデフォルトエクスポートで再エクスポート
export { foo as default } from './my-module.js';
```

#### すべてをインポート

```js
import * as myModule from './my-module.js';
// my-module.js で foo という名前でエクスポートされたもの
console.log(myMoudle.foo);
// my-module.js デフォルトエクスポートされたもの
console.log(myMoudle.default);
```

#### 副作用のためのインポート

モジュール内のトップレベルな処理を実行させたいとき、副作用のためのインポート構文を用いる。

```js
// side-effecs.js
window.foo = 'foo';
```

```js
// ./side-effects.js のグローバルコードが実行される
import './side-effects.js';
```

### ECMAScript モジュールを実行する

クライアントサイドで実行させる場合、以下のように JavaScript ファイルを ECMAScript モジュールとしてウェブブラウザに読み込ませる必要がある。

```html
<script type="module" src="./my-module.js"></script>
<script type="module">
import { foo } from './my-module.js';
</script>
```

## 1.28 ECMAScript

- ECMAScript は [ECMA International](http://www.ecma-international.org/) という団体によって標準化されている
- C# や Dart も ECMA International の中で標準化が行われている
- ECMA International の中の Technical Committee 39 (TC39) という委員会が中心となって ECMAScript の仕様について議論している

### ECMAScript のバージョンの歴史

| バージョン         | リリース時期 |
| ------------------ | ------------ |
| 1                  | 1997.6       |
| 2                  | 1998.6       |
| 3                  | 1999.12      |
| 4                  | 破棄         |
| 5                  | 2009.12      |
| 5.1                | 2011.6       |
| 2015 (6)           | 2015.6       |
| 2016 (7)           | 2016.6       |
| 2017               | 2017.6       |
| 以降、毎年リリース |              |

### Living Standard となる ECMAScript

- ECMAScript の仕様書のドラフトは [tc39/ecma262](https://github.com/tc39/ecma262) で管理されている
- 最新の仕様書は https://tc39.github.io/ecma262/ にある
    - 更新ごとにバージョン番号をつけず、常に最新版公開する  (Living Standard)
    - ES2017 のようにその年をバージョン番号としてつけたものは、Living Standard のスナップショットのようなもの

### 仕様策定のプロセス

- ES2015 まではすべての仕様の合意が取れるまで延々と議論を続け、すべてが決まってからリリースしていた

    - リリースまで長い時間を要し、言語の進化が停滞していた

- ES2016 以降は毎年新仕様をリリースする形へ策定プロセスを変更した

- 仕様に追加する機能はそれぞれのプロポーザル (提案書) ごとに進められている

    - [tc39/proposals](https://github.com/tc39/proposals) に一覧が公開されている
    - [ts39-notes](https://github.com/tc39/tc39-notes) に議事録が公開されている
    - それぞれのプロポーザルは責任者であるチャンピオンとステージという0~4の5段階の状態を持つ

    | ステージ | ステージの概要                                               |
    | -------- | ------------------------------------------------------------ |
    | 0        | アイデア                                                     |
    | 1        | 機能提案                                                     |
    | 2        | 仕様書のドラフトを作成                                       |
    | 3        | 仕様としては完成し、ブラウザの実装・フィードバックを求める   |
    | 4        | 仕様策定が完了し、2つ以上の実装が存在していて ECMAScript にマージできる段階 |

- ES4 では多くの変更を試みたが、意見がまとまらず結果的に数年分のリソースが無駄になってしまったことへの反省もあり、合意が取れる最小限の範囲でプロポーザルが管理されている

### プロポーザルの機能を試す

- Stage 4 の段階で2つ以上の実装が必要なため、仕様策定中のプロポーザルがブラウザや Transpiler, Polyfill という形で試すことができる
    - code to code の変換を行うものを Transpiler という (TypeScript や Babel など)
    - 新しい仕様の実装を提供するライブラリを Polyfill という (core-js や polyfill.io など)
- Transpiler や Polyfill は完全に新しい機能を再現できていない場合もあるので新しい機能の学習のために使うべきではない

### 仕様や策定プロセスを知る意味

- 言語を学ぶため
    - JavaScript の言語機能を参照する目的ならば https://developer.mozilla.org/ja/ などが有用な場合もある
- 言語が進化しているため
    - 日々更新されていく機能を知るため
- 情報の正しい状態を調べるため
    - JavaScript は幅広く使われている言語で、ネット上には玉石混合の情報がある
    - 一次ソースとして仕様をあたることは有効

# 疑問

- エラーハンドリング戦略
