---
layout: ../../layouts/index.astro
---

# 応用編 (ユースケース)

## アプリケーション開発の準備

### Node.js のインストール

Node.js の特徴は以下のようなものがある。

- V8 JavaScript エンジンで動作 (Web ブラウザの Google Chrome と同様)
- オープンソース (MIT ライセンス)
- OS を問わずクロスプラットフォームで動作
- サーバーサイド、CLI ツール、Electron (デスクトップアプリ) など幅広く使われる
- LTS (Long-Term Support) 版と最新版の2つのリリース版がある
  - LTS では2年間のメンテンナンス、サポートが宣言され、後方互換性が守られる
  - 最新版では最新の機能を試すことができるが、最新バージョンのみがメンテナンスされる
- npm というパッケージマネージャが同梱される

### npx コマンドによる npm パッケージの実行

npm をインストールすると `npx` というコマンドも使用できるようになる。`npx` コマンドでは `npm install` することなく Node.js の CLI ツールを直接実行できる。

```bash
npx create-react-app my-app
```

### コマンドラインツールのインストールと実行

`npm install` でカレントディレクトリにパッケージをインストールする。`--global` (`-g`) フラグをつけると、グローバルにパッケージをインストールし、シェルから直接実行できる。

```bash
npm install --global ts-node
```

### ローカルサーバーのセットアップ

- ローカルのファイルをブラウザから読み込むのではなく、`http` スキーマからアクセスできるようにする
  - `file:///` から始まる URL では Same Origin Policy のセキュリティ制限のため開発を行う上で制約になる

## Ajax 通信

### エントリーポイント

### ウェブブラウザと DOM

- HTML ドキュメントのコンテンツと構造を JavaScript で操作できるようにしたオブジェクトを DOM (Document Object Model) という
- DOM では HTML タグの入れ子関係を木構造で表現するため、HTML タグの木構造をDOM ツリーという
- DOM API は Web ブラウザが実装している API

### HTTP 通信

#### Fetch API

- XMLHttpRequest と似た API を持つが、より強力で柔軟
- URL を与えてHTTPリクエストを送ることができる

```js
fetch(`https://api.github.com/users/${encodeURIComponent(uesrId)}`);
```

#### レスポンスの受け取り

`response.json()` も `Promise` を返すことに注意。

```js
function fetchUser() {
     fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
        .then((response) => {
            return response.json().then((user) => {
                console.log(user);
            });
        });
}
```

#### XMLHttpRequest

- Fetch API が標準化されるまでは広く使われていた
- ブラウザ互換のため XHR を使う場面も存在する

```js
function fetchUser(userId) {
    const request = new XMLHttpRequest();
    request.open('GET', `https://api.github.com/users/${encodeURIComponent(userId)}`);
    request.addEventListener('load', () => {
        if (request.status >= 200 && request.status < 300) {
            console.log(JSON.parse(request.responseText));
        } else {
            console.error(request.statusText);
        }
    });
    request.addEventListener('error', () => {
        console.error('Network error');
    });
    request.send();
}
```

### データを表示する

#### HTML を組み立てる

改行が可能なテンプレートリテラルを使用すると見通しが良くなる。

```js
const view = `
<h4>${user.name} (@${user.login})</h4>
<img src="${user.avatar_url}" alt="${user.login}" height="100">
`;
```

#### HTML 文字列を DOM に追加する

- `Element#innerHTML` プロパティにセットする
- `Element` オブジェクトを生成して、`Node.appendChild` などで手続き的に DOM ツリーを構築する

セキュリティ的には後者の方法の方が安全だが、冗長になるため、以下のようにプロパティにセットする方法で実装する。

```js
const result = document.getElementById('result');
result.innerHTML = view;
```

#### HTML 文字列をエスケープする

- `Element#innerHTML` に文字列をセットする際、文字列内に HTML として有効な記号 (`<` や `>`)  などが含まれていると意図しない構造になる可能性がある
- 意図しない構造の HTML になるのを防ぐため、特定の記号をエスケープ (HTML エスケープ ) する必要がある
  - 一般的にはライブラリ等が提供する機能を用いることが多い

```js
function escapeSpecialChars(str) {
    return str
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#039;');
}
```

この関数を HTML を組み立てているテンプレートリテラル内の変数すべてに対して呼び出すのは面倒なので、テンプレートリテラルをタグ付けすることで値の埋め込み際に明示的に関数を呼び出す必要がないようにする。

```js
function escapeHTML(strings, ...values) {
    return strings.reduce((res, str, i) => {
        const value = values[i - 1];
        return typeof value === 'string'
            ? result + escapeSpecialChars(value) + str
            : result + String(value) + str;
    });
}

// タグつきテンプレート関数の形式で呼び出す
const view = escapeHTML`
<h4>${user.name} (@${user.login})</h4>
<img src="${user.avatar_url}" alt="${user.login}" height="100">
`;
```

### Promise を活用する

#### Promise  チェーン

- Promise チェーンの中で投げられた例外は `catch` メソッドで一カ所で受け取れる
- Promiser チェーンでは `then` に渡されたコールバック関数の返り値を、 Promise である場合はそれを解決した値を、Promise でない場合はそのままの値を次の `then` に渡す
  - Promise チェーン内では非同期と同期を意識せず使える

#### Async Function

```js
// Promise チェーン
function main1() {
     fetchUserInfo('js-primer-example')
        // ここではJSONオブジェクトで解決されるPromise
        .then((userInfo) => createView(userInfo))
        // ここではHTML文字列で解決されるPromise
        .then((view) => displayView(view))
        // Promiseチェーンでエラーがあった場合はキャッチされる
        .catch((error) => {
            console.error(`エラーが発生しました (${error})`);
        });
}

// Async Function
async function main2() {
    try {
        const user = await fetchUserInfo('js-primer-example')
        const view = createView(user);
        displayView(view);
    } catch (err) {
        console.error(`エラーが発生しました (${err})`);
    }
}
```

## Node.js で CLI アプリ

### Node.js で Hello World

Node.js で

```js
console.log('Hello World');
```

とすると、標準出力に文字列が出力くされる。

### Node.js とブラウザのグローバルオブジェクト

- Node.js は Google Chrome と同様に JavaScript エンジンとして V8 が使用されている
- ブラウザと Node.js では利用できるグローバルオブジェクトが違うということを意識する必要がある
  - ブラウザ: `document`, `XMLHttpRequest`, `fetch`
  - Node.js: `process`, `Buffer`
- 逆に、ECMAScript に定義されていないが、ブラウザと Node.js に共通に実装されている機能もある
  - Console API
  - `setTimeout` 関数

### コマンドライン引数を処理する

#### `process` オブジェクトとコマンドライン引数

```js
// main.js
// コンソールにコマンドライン引数を出力
console.log(process.argv);
```

```bash
$ node main.js one two=three four
[
  '~/.nodenv/versions/16.1.0/bin/node',
  '~/nodecli/main.js',
  'one',        # 1番目の引数
  'two=three',  # 2番目の引数
  'four'        # 3番目の引数
]
```

#### commander パッケージをインストールする

```bash
npm i commander # 最新版をインストール
npm i commander@5.0 # 5.0.x に一致する最新のバージョンをインストール
```

#### CommonJS モジュール

- Node.js 環境で使われているモジュール形式に CommonJS がある
- `module.exports` というオブジェクトに代入された値がエクスポートされ、`require` 関数でインポートできる

```js
// my-module.js
module.exports = {
    foo: 'foo',
};
```

```js
const myModule = require('./my-module');
console.log(myModule.foo); // => 'foo'
```

### ファイルを読み込む

#### `fs` モジュールを使ってファイルを読み込む

```md
# sample
```

#### `fs` モジュール

- `readFile` / `readFileSync` 関数は引数でファイルの読み込み方を変えることができる
  - デフォルトではバイト列 (`Buffer` インスタンス) を返すため、`utf-8` で読み込みたい場合は明示的にエンコード方式を指定する必要がある

```js
const fs = require('fs');
// 非同期
fs.readFile('sample.md', { encoding: 'utf-8' }, (err, file) => {
    if (err != null) {
        console.error(err);
    } else {
        console.log(file);
    }
});

// 同期
try {
    const file = fs.readFileSync('sample.md', { encoding: 'utf-8' });
    console.log(file);
} catch (err) {
    console.error(err);
}
```

### Markdown を HTML に変換する

#### `marked` パッケージを使う

```bash
npm i marked
```

markd パッケージでマークダウンのテキストを HTML に変換することができる

#### 変換オプションを作成する

##### `gfm` オプションをコマンドライン引数から受け取る

GitHub におけるマークダウンの仕様である GitHub Flavored Markdown に合わせて変換するかをオプションで渡せるようにする。

```bash
node main.js --gfm sample.md
```

```js
const program = require('commander');
const fs = require('fs');
const marked = require('marked');

// gfm オプションを定義する
program.option('--gfm', 'GitHub Flavored Markdown を有効にする');
program.parse(process.argv);
const filePath = program.args[0];
// コマンドライン引数のオプションを取得する
const options = program.opts();
fs.readFile(filePath, { encoding: 'utf-8' }, (err, file) => {
    if (err != null) {
        console.error(err.message);
        return process.exit(1);
    }
    const html = marked(file, {
        gfm: options.gfm ?? false,
    });
    console.log(html);
});
```

### ユニットテストを記述する

#### ユニットテスト実行環境を作る

- テスティングフレームワークに Mocha を使う
- Node.js 標準のモジュールである `assert` モジュールの `assert.strictEqual` を使用する

```bash
npm install --save-dev mocha@7
```

```json
{
    "scripts": {
        "test": "mocha test/"
    }
}
```

#### ユニットテストを記述する

```js
const assert = require('assert');
const fs = require('fs');
const path = require('path');
const md2html = require('../md2.html');

it('converts Markdown to HTML (GFM=false)', () => {
    const sample = fs.readFileSync(path.resolve(__dirname, './fixtures/sample.md'), { encoding: 'utf8' });
    const expected = fs.readFileSync(path.resolve(__dirname, './fixtures/expected.html'), { encoding: 'utf8' });
    assert.strictEqual(md2html(sample, { gfm: false }).trimEnd(), expected.trimEnd());
});

it('converts Markdown to HTML (GFM=true)', () => {
    const sample = fs.readFileSync(path.resolve(__dirname, './fixtures/sample.md'), { encoding: 'utf-8' });
    const expected = fs.readFileSync(path.resolve(__dirname, './fixtures/expected.html'), { encoding: 'utf8' });
    assert.strictEqual(md2html(sample, { gfm: false }).trimEnd(), expected.trimEnd());
});
```

#### なぜユニットテストを行うのか

- 早期にバグが発見できる
- 安心してリファクタリングできる
- テストしやすいコードを書くことが適切なモジュール化の指針になる
- 満たすべき仕様を示すドキュメントとしての役割

## Todo アプリ

### エントリーポイント

- `<script type="module">` で読み込まれたファイルは `<script>` 要素ごとに別々のモジュールスコープをもつ
  - `import` 文で他のモジュールを読み込む必要がある
- `index.html` によって読み込まれる `index.js` をエントリーポイントとする

### アプリの構成要素

#### Todo アプリの構造を HTML で定義する

- class 属性
  - 基本的に CSS から装飾を行うための目印として使われる
  - 1つのページの中で同じクラス名を複数の要素に対して設定できる
- id 属性
  - 基本的にページ内でユニークな識別子をつけるための属性
  - CSS や JavaScript から参照するための目印、リンクのアンカなど様々な用途で使用される

```css
/* クラス名で指定 */
.todo-app {
    background: black;
}
/* id 属性で指定 */
#todoList {
    color: white;
}
```

#### 入力内容をコンソールに表示する

```js
export class App {
    mount() {
        const formElement = document.querySelector('#js-form');
        const inputElement = document.querySelector('#js-form-input');
        formElement.addEventListener('submit', (event) => {
            event.preventDefault();
            console.log(`入力欄の値: ${inputElement.value}`);
        });
    }
}
```

### イベントとモデル

#### 直接 DOM を更新する問題

- アプリケーション内の状態 (どのようなアイテムを持つかなど) が DOM 上にしか存在しないことになる
- HTML 要素には文字列しか埋め込めないため、オブジェクトのようなデータは DOM 上で保持することができない
- 操作に対して更新する表示箇所が増えてくると表示の処理が複雑化する

#### モデルを導入する

- アプリケーションが保持している情報を JavaScript クラスとしてモデル化する
  - 状態をインスタンスのプロパティで管理
  - プロパティはメソッド (`addItem`, `getAllItems` など) 経由で行う
- 操作に対するモデルの処理は様々だが、捜査に対する表示の処理はどの場合も同じになる
  - 表示箇所が増えても複雑さを一定に保てる

#### モデルの変化を伝えるイベント

1. `TodoListModel` の状態が変化したら自分自身へ `change` イベントを発生 (ディスパッチ)
2. 表示側はそのイベントをリッスンしてイベントが発生したら表示を更新する

DOM API のイベントの仕組みをモデルでも利用できれば、モデルの更新を表示の更新につなげることができる。

#### `EventEmitter`

- イベントの仕組みは「イベントをディスパッチする側」と「イベントをリッスンする側」の2つの面から成り立つ
- → イベントをディスパッチ (発生) したときにイベントをリッスンしているコールバック関数 (イベントリスナー) を呼び出す

これらを満たす `EventEmitter` というクラスを作成し、それを継承する形で `TodoListModel` を作成する。

##### `EventEmitter` を継承した `TodoList` モデル

- `EventEmitter` クラスを継承した `TodoListModel` クラスを作成
- `TodoItemModel` クラスを作成し、`TodoListModel` が複数の `TodoItemModel` をもつようにする

## Todo アイテムの更新と削除を実装する

### Todo アイテムの更新

- `<input type="checkbox">` は `checked` 属性の有無によってチェックボックスを管理している
- モデル内の状態と表示の整合性をとるために、チェックボックスがチェックされたらモデルの状態を更新する必要がある
- `<input type="checkbox">` 要素がチェックされたときにディスパッチする `change` イベントをリッスンするコールバック関数を登録して、そのコールバック関数内でモデルを更新する処理を記述する
  - Todo アイテムの完了状態を更新する処理を書く

#### Todo アイテムの削除

- `TodoListModel` に Todo アイテムを削除するメソッドを定義する
- 削除ボタンの `click` イベントが発生したら`TodoListModel` 内で管理されている Todo アイテムを削除する

### Todo アプリのリファクタリング

- `App.js` 内の処理が肥大化している
- HTML の表示に課する処理がほとんどをしめている

#### View コンポーネント

- `TodoItemModel` の配列に対する HTML 要素の作成が `App.js` の多くを占めているため、モジュール化する
- HTML の表示を担う View コンポーネントを定義する
  - このアプリでは `TodoItemView` (Todo アイテム View コンポーネント), `TodoListView` (Todo リスト View コンポーネント) を定義する

#### `TodoItemView` / `TodoListView`

- `onUpdateTodo` と `onDeleteTodo` というリスナー関数を外部から受け取って、更新 / 削除時に受け取った関数を実行させる
  - 具体的な更新 / 削除の処理は View クラスの外部に定義できる

#### App のリファクタリング

- `onUpdateTodo` のコールバック関数では `TodoListModel#updateTodo` メソッドを呼ぶ
- `onDeleteTodo` のコールバック関数では `TodoListModel#deleteTodo` メソッドを呼ぶ

#### App のイベントリスナーを整理する

| イベントの流れ | リスナー関数                                       | 役割                                                    |
| -------------- | -------------------------------------------------- | ------------------------------------------------------- |
| Model → View   | `this.todoListModel.onChange(listener)`            | `TodoListModel` が変更イベントを受け取る                |
| View → Model   | `formElement.addEventListener('submit', listener)` | フォームの送信イベントを受け取る                        |
| View → Model   | `onUpdateTodo`: listener                           | Todo アイテムのチェックボックスの更新イベントを受け取る |
| View → Model   | `onDeleteTodo`: listener                           | Todo アイテムの削除イベントを受け取る                   |
