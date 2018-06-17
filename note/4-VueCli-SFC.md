# 勉強会のゴール
- [ ] Vueのコンポーネントシステムについて理解する
- [ ] 単一ファイルコンポーネントの使い方を理解する
- [ ] `vue-cli`でSPA(Single Page Application)を作成できるようになる

# Vueのコンポーネントシステムについて
Vue.jsでは基本的なHTML要素(JS, CSS含む)をカプセル化して、再利用性の高いコードを記述することができます。

### コンポーネントの構成
コンポーネントは隔離されたスコープ(isolated scope)を持つため、基本的に親要素や他のコンポーネントが持つ値やメソッドにアクセスできません。プロパティとして受け取った値( `props`)に対してコンポーネント内の固有のメソッドを適用し、最終的に  `template`を親要素に返すというのが主な用途になります。

`template`:
コンポーネントの呼び出し部分に挿入されるDOM要素を定義します

`props`:
親要素から伝達される値を定義します。 `props`で定義しない限り親要素から伝達される値は利用できません。データ型のValidation, デフォルト値等も定義することが可能です。

```js
Vue.component('example', {
  props: {
    // 基本な型チェック (`null` はどんな型でも受け付ける)
    propA: Number,
    // 複数の受け入れ可能な型
    propB: [String, Number],
    // 必須な文字列
    propC: {
      type: String,
      required: true
    },
    // デフォルト値
    propD: {
      type: Number,
      default: 100
    },
    // オブジェクトと配列のデフォルトはファクトリ関数から返すようにしています
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // カスタムバリデータ関数
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

**注意！！**
コンポーネント内に `data`プロパティを定義する際は、オブジェクトとしてではなく**関数として**定義してください。これはコンポーネントを複数回呼び出したり、親コンポーネントに `data`が定義されていた際に干渉しあうことを防ぐ目的です。詳細は[公式ドキュメント](https://jp.vuejs.org/v2/guide/components.html#data-は関数でなければならない)を参照してください。

### コンポーネントの登録
作成したコンポーネントを利用するには
- グローバルに登録する
- 特定のインスタンスに対してローカル登録する

という2つの方法が用意されています。
インスタンスの保守性を優先するため、Warriorではローカル登録を推奨しています。

グローバルに登録する方法：
`Vue.component(tagName, options)`という記法を用います
```js
Vue.component(my-component, {
  template: ,
  props: 
})
```

ローカルに登録する方法：
`Vue`インスタンスに用意された `components`プロパティを利用します
```js
const myComponent = {
  template: ,
  props: ,
}

new Vue({
  el: '#app',
  components: {
    'my-component': myComponent
  }
})
```

### コンポーネントの呼び出し

### 単一方向データフロー
状態の一貫性を保つためにプロパティを通したデータの伝達は親から子に対して一方向で行われ、子が親の状態に変更を加えることは許容されていません。
子要素からグローバルな値に変更を加える際はVuex等Fluxフレームワークの利用が推奨されます

### Slotの利用
テンプレート内にて `slot`要素を用いることで任意の部分に親要素からDOMを注入することが可能です。 `name`を用いて対象のSlotを選択します。

```js
// 子コンポーネント
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

```js
// 親コンポーネント
<app-layout>
  <h1 slot="header">Here might be a page title</h1>
  <p slot="footer">Here's some contact info</p>
</app-layout>
```

```html
// 描画結果
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
````

# 単一ファイルコンポーネント
通常のJSファイル内でコンポーネントを定義しようとすると、
- 可読性が低い
- インラインでしかCSSが記述できない
- プリプロセッサ(JSX, Jade, Pug, Sass)を利用できない

等の問題が発生します。
この問題を解決するためにVueでは `.vue`拡張子のファイルによるコンポーネント定義がサポートされ、 `Webpack`および `Browserify`向けの変換モジュールが公式で提供されています。

```js
<template>
  <li>
    {{ item }}
  </li>
</template>

<script>
  export default {
    props: {
      item: {
        type: String,
        required: true 
      } 
    }
  }
</script>

<style lang="sass">
  $blue: 'blue';

  li {
    color: $blue
  }
</style>
```

# vue-cli
[vue-cli](https://github.com/vuejs/vue-cli)はVue.jsを仕様したプロジェクトのscaffolding(骨組みづくり)をしてくれる公式のCLIツールです。

ボイラープレート(雛形)を指定することで開発に必要なツール(Webpack, vue-router, eslint)が既に揃った状態でアプリケーションを作成できる非常に協力なツールです。

`vue-cli`のインストール
```shell
  $ yarn global add vue-cli
```

プロジェクトの立ち上げには `vue init <template name> <project name>`というコマンドを使用します。
```shell
$ vue init webpack sample
```

現在公式で提供されている雛形の一覧は[こちら](https://github.com/vuejs-templates)から確認できます。

デフォルトでいくつかのNPM scriptが用意されているので、以下から各コマンドを確認してください

|コマンド名|機能|
|---|---|
|dev|開発用サーバーの起動。HMRが利用できる|
|build|アプリケーションを `/dist`以下に静的ファイルとして書き出し。Uglify, minify|
|unit|unitテストの実行|
|e2e|e2eテストの実行|
|test|unit & e2eの実行|
|lint|eslintによる構文の静的チェック|
