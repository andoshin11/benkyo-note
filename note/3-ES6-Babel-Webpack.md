# 本日のゴール
- [ ] `Yarn`でパッケージの追加、削除ができるようになる
- [ ] `ES6`が読めるようになる
- [ ] `Babel`で `ES6`をトランスパイルできるようになる
- [ ] `Webpack`でモジュールバンドリングができるようになる
- [ ] `babel-loader`, `sass-loader`が使えるようになる
- [ ] `webpack-dev-server`を利用してHMRを活用した開発ができるようになる

**1日で理解するのは無理なので、今後目にした時の心理的障壁を下げるのが目的です！！**

# Yarn
`Yarn`とはFacebookが開発したJavaScriptのパッケージマネージャーです。

- 依存パッケージのバージョンまでは保証されない
- パッケージがインストール時にコードを実行することを許可しているため、セキュリティー上の問題がある
- タスクが並列実行されない(パッケージが1つずつしかインストールされない)

といった `npm`の問題を解決するために開発されました。
`Yarn`を通じてインストールされたパッケージは `./node_modules`以下に格納されます。 `.gitignore`の更新を忘れないようにしましょう。

### Yarnの使い方

`Yarn`の初期化

```shell
$ yarn init
```

`package.json`で指定されたパッケージをインストールする

```shell
$ yarn install
```

パッケージの一覧を依存関係とともに表示する

```shell
$ yarn list
```

新しくパッケージを追加する

```shell
$ yarn add <package name>
```

パッケージをGlobalで追加する

```shell
$ yarn global add <package name>
```

パッケージを削除する

```shell
$ yarn remove <package name>
```

パッケージをアップグレードする

```shell
$ yarn upgrade <package name>
```

`package.json`で定義されたスクリプトを実行する

```shell
$ yarn run <script name>
```

# ES6
`ES6`( `ECMAScript2015`)とは2015年に公式に策定されたJavaScriptの新しい標準仕様。今までのもの( `ES5`)に対して大幅な機能追加が行われたため、ブラウザの対応状況はまちまち
https://kangax.github.io/compat-table/es6/

### ES6の主な機能
- `let`, `const`による変数宣言
`var`による変数宣言はグローバル汚染を引き起こすので原則使わない。 `let`, `const`のスコープはブロック内のみ。 `let`は再代入可能だが、 `const`は再代入不可の変数。

![letとconstによる変数宣言](https://gyazo.com/84f6337e2a3d2f362a5c8e1f13a159d1.png)

- テンプレートリテラル
バッククォートで囲まれた文字列では `${hoge}`という記法で変数を文字列内に展開することができる

![テンプレートリテラル](https://gyazo.com/6c22266bbedfa55b53d8f8c8b2becd0c.png)

- 関数へのデフォルト引数
関数定義の際に、引数のデフォルト値を定義することができる

![デフォルト引数](https://gyazo.com/f9ed2cdd0d80ce1d0ef4546db8db62e6.png)

- 引数に可変長パラメータを指定する
関数定義の際に、不特定多数の値を受け取って関数内で配列として利用できます

![レストパラメーター](https://gyazo.com/2dc27068b779b74acd4dcb855b08622b.png)

- アロー関数
```js
// 通常の関数定義
function normalFunc(hoge, fuga) {
  var message = hoge + fuga;
  return message;
}

// アロー関数による関数定義
arrowFunc((hoge, fuga) => {
  const message = hoge + fuga
  return message
})

// 引数が1つの場合は括弧を省略できます
arrowFuncShort(hoge => {
  return hoge
})

// 処理によっては1行で記述できます
// 1行で記述した際は最後の値が自動的にreturnされます
arrowFuncSimple((hoge, fuga) => hoge + fuga)
```

- プロパティのショートハンド
オブジェクトのkey名とvalueの変数名が同じ場合は記述を省略できます。また、関数についても簡潔に記述できるようになりました
```js
var person = {
  firstName: firstName,
  lastName: lastName,
  age: age,
  fullName: function () {
    return this.firstName + ' ' + this.lastName
  }
}

// 上のオブジェクトと同じ構造になります
const person = {
  firstName,
  lastName,
  age,
  fullName () => `${this.firstName} ${this.lastName}`
}

```

- スプレッド演算子
複数要素を持ったオブジェクト(配列等)の先頭に `...`をつけることで式展開できる

![スプレッド演算子](https://gyazo.com/f70e22c1da1fd41eaee12016e7cc6960.png)


- Promise : 非同期処理を行うためのオブジェクト(次々回)
- Class構文(次回)

Common.js:
ブラウザ以外でも動くように設計されたもの。Node.js等で使われる。module.exportsやrequire等の機能もあるが、ブラウザでは動かない

# Babel
ブラウザのサポートを待たずにES6で開発するためのツール。ES6以降の構文で記述されたJSをES5で動く形式に変換してくれる。

### Babelの使い方
```shell
// babel-cliをインストール
$ yarn global add babel-cli

// 対象のpresetをインストール。
$ yarn init
$ yarn add babel-preset-es2015

// 設定ファイルを追加
$ touch .babelrc
```

`.babelrc`に記述

```.babelrc
// .babelrc
{
  "presets": ["es2015"]
}
```

ES6で作成したJSを作成
```app.js
// app.js
class App {
  constructor() {
    console.log('class syntax');
  }
}

const func = () => {
  console.log('arrow function');
};
```

`babel`コマンドで変換

```shell
$ babel app.js -o es5.js
```

ES5に変換されたファイルが出力される

```
// es5.js
'use strict';

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

var App = function App() {
  _classCallCheck(this, App);

  console.log('class syntax');
};

var func = function func() {
  console.log('arrow function');
};
```

`--watch`オプションでファイルが更新されるたびに更新することも可能

```shell
$ babel app.js --watch -o es5.js
```

### Tower of Babel
Babelの機能を試すためのチュートリアルツール
https://github.com/yosuke-furukawa/tower-of-babel/issues

```shell
$ yarn global add tower-of-babel
$ tower-of-babel
```

# Webpack
`Webpack`とはモジュールバンドラーとして複数のファイルを1つにまとめてくれるものです。他のモジュールバンドラーとしてはBrowserifyなどがあります。


現代のフロントエンド開発: 
・機能単位でファイルを分割(モジュール)して開発
・実際に動かす際は1つにまとめて(バンドリング)から `script`タグで読み込み

というのがここ最近のスタンダードな手法です。複数のバンドルファイルを生成することをビルドと呼びます

![](https://camo.qiitausercontent.com/b3492517f986482040c941a7e4089535840b5c18/68747470733a2f2f71696974612d696d6167652d73746f72652e73332e616d617a6f6e6177732e636f6d2f302f36393636372f34323537643039632d303366612d663935322d646437302d6135376437376630663231332e6a706567)


1. Entory pointとなるファイルの準備
2. import/exportを活用して依存関係を構築
3. Webpackでbundle.jsを生成

という流れで開発を進めていきます。

### ES module
Node.js(= Common.js)においては `require`と `module.exports`によるファイル分割が可能ですが、ECMA Script2015では独自の構文が定義されています。
現在は構文が定義されているのみで、実装については進んでいない状態なのでその構文が利用されているファイルをWebpackがバンドリングしてくれます

### Webpackを利用するメリット
- ファイル分割により、JSのメンテコストが下がる
- ブラウザからの読み込み対象が1つになることでリクエスト数が減る
- Yarnでインストールした外部ライブラリを自分のコードで簡単に呼び出せる。scriptタグの順番を気にしなくても依存関係を解決したコードを出力してくれる

### Webpackの使い方
「グローバルにインストールしてコマンドを叩く」 
or 
「node_modules以下のバイナリを直接叩くYarnスクリプトを設定する」

という2つの使い方(`babel`は前者の呼び方をした)がありますが、後者の方がポータビリティが高いのでチーム開発においては良いです。

`Yarn`で `webpack`をインストール

```shell
$ yarn add webpack
```

`webpack.config.js`を作成し、以下のようにディレクトリを設定する
```shell
.
├── dist
│   └── js
├── index.html
├── package.json
├── src
│   └── js
│       └── app.js
├── webpack.config.js
└── yarn.lock

4 directories, 5 files
```

webpack.config.jsに以下の内容を記述

```webpack.config.js
// webpack.config.js
var path = require('path');

module.exports = {
  // エントリーポイントの設定
  entry: './src/js/app.js',
  // 出力の設定
  output: {
    // 出力するファイル名
    filename: 'bundle.js',
    // 出力先の絶対パス
    path: path.join(__dirname, 'dist/js')
  }
};
```

1. pathモジュールの読み込み: 絶対Pathの取得に利用
2. エントリーポイントの設定: ここで指定したファイルから依存解決を開始する
3. 出力先ファイル名の設定
4. 出力先ディレクトリの設定: 絶対パスで指定する必要がある

`./src/js/foo.js`を作成し、 `./src/js/app.js`に `import`する

```foo.js
// foo.js
export var foo = 'Foo';
```

```app.js
// app.js
import { foo } from './foo';
console.log(foo);
```

`webpack`コマンドを実行すると、 `./dist/js/bundle.js`が生成されることが確認できる

```shell
$ ./node_modules/.bin/webpack 
```

実際にHTMLから `bundle.js`を読み込み、動作することを確認する

```index.html
// index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <script src="./dist/js/bundle.js"></script>
  <title>Document</title>
</head>
<body>
  Hello World!
</body>
</html>
```

![bundle.js](https://gyazo.com/347ce2d9dcb7300a87fb4298edd4d869.png)

また、 `webpack`コマンドを呼び出しやすいように `Yarn script`を設定する

```package.json
// package.json
{
  "name": "20171130",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "build": "./node_modules/.bin/webpack"
  },
  "dependencies": {
    "webpack": "^3.8.1"
  }
}
```

スクリプトが実行できることを確認

```shell
$ yarn run build
```

### Loaders
Webpackは依存関係のある全てのファイルを解析するが、その際に任意のファイルをLoaderに渡すことができる

`babel-loader`:
ES6のコードをES5のコードに変換してくれる。

必要なパッケージをインストール

```shell
$ yarn add babel-core babel-loader babel-preset-es2015
```

Webpackの設定を更新。Babelの設定もここに記述する。

```webpack.config.js
var path = require('path');

module.exports = {
  entry: './src/js/app.js',
  output: {
    filename: 'bundle.js',
    path: path.join(__dirname, 'dist/js')
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "babel-loader",
        query:{
          presets:['es2015']
        }
      }
    ]
  }
};
```

`test`: 正規表現でloaderの適用対象を指定
`exclude`: `/node_modules`以下のファイルにはloaderを適用しない
`loader`: 適用するloaderを指定
`query`: `babel-loader`コマンドの実行時に渡す引数を指定

`app.js`にアロー関数を追加
```app.js
// app.js
import { foo } from './foo';
console.log(foo);

const sayFoo = name => `${name} says Foo`;

console.log(sayFoo('Shin'));
```

`$ yarn run build`で再ビルドし、関数が動くことを確認

![](https://gyazo.com/60bd454afaa26add39b4a52502100436.png)

`sass-loader`:
`scss`をコンパイルしてくれる。WebpackではLoaderを通すことでCSSや画像ファイルなどもバンドリングすることが可能になります。

必要なパッケージをインストール

```shell
$ yarn add sass-loader css-loader style-loader node-sass
```

`Webpack`の設定を更新。`.scss`向けのloaderを追加する

```webpack.config.js
// webpack.config.js
...
      {
        test: /\.scss/, // 対象となるファイルの拡張子
        loaders: [
          'style-loader',
          {
            loader: 'css-loader',
            // オプションでCSS内のurl()メソッドの取り込みを禁止する
            options: {url: false}
          },
          'sass-loader'
        ]
      }
...
```

JavaScript内で `.scss`ファイルをimportする

```app.js
// app.js
import './../scss/application.scss'
```


`eslint-loader`:
コードを最適に解析してビルド前に構文エラーを検知してくれる


# 応用編
### webpack --watch
ファイルの変更を検知して自動でリビルドしてくれる。 `Yarn script`に以下のコマンドを追加。

```package.json
// package.json
...
  "scripts": {
    "build": "./node_modules/.bin/webpack",
    "watch": "./node_modules/.bin/webpack --watch"
  },
...
```

`$ yarn run watch`を実行して `watch`モードで立ち上げると、毎回ビルドコマンドを実行しなくても、ファイルの変更を検知して自動で `bundle.js`を差分ビルドしてくれる

### webpack-dev-server
いつかやります！！
