# 今回のゴール
- 単一方向データフローとその問題点を理解する
- Fluxアーキテクチャーを理解する
- Vuexの使い方を学ぶ

# Vueのデータフロー
基本的には単一方向のデータフロー。 `props`を用いて親から子コンポーネントにデータや状態を渡す

```javascript
// parent.vue

<template>
  <ul>
    <Child :item="item" v-for="item in itemList">
  </ul>
</template>

<script>
  import Child from './Child.vue'
  export default {
    components: {
       Child
    },
    data () {
      return {
        itemList: ["apple", "banana", "orange", "strawberry"]
      }
    }
  }
</script>
```

```javascript
// child.vue
<template>
  <li>{{ item }}</li>
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
```

# 単一方向データフローの問題点
- 親のデータを変更することが難しい(できなくはない)
- 兄弟のデータを変更することが難しい
- 孫へのデータの受け渡し方法が冗長になる

力技で実現しようとするとメンテナンス性の低いコードになってしまう

↓

Vuex: グローバルなストア + Fluxアーキテクチャで、アプリケーションの状態を一限管理する仕組み

# Fluxアーキテクチャー
> Flux is a pattern for managing data flow in your application

とあるように、Fluxとはデータ管理パターンそのものを指す概念。以下の四つの要素から成り立つ。

- Dispatcher
- Store
- Action
- View

![](https://github.com/facebook/flux/raw/master/examples/flux-concepts/flux-simple-f8-diagram-with-client-action-1300w.png)

## Dispatcher
Actionの内容をStoreに適用する仕組み

## Store
アプリケーションの `Single source of truth`となるデータ保管庫。Actionによってのみ変更(mutate)される。それ以外の方法で変更を加えることはできない。変更が加えられると `change`イベントを発火する

## Action
Storeに変更を加える際のAPIとして機能する。通例的に `type`と `payload`を持ち、変更内容をセマンティックに表現する

## View
Storeのデータをユーザーに表現する仕組み。Storeに変更が加わると、その内容をリアクティブに反映する。

## 主なFlux実装
Fluxの概念を取り入れたライブラリとしてはRedux, Mobx, Rx等が有名です

# Vuexの思想
![](https://vuex.vuejs.org/ja/images/vuex.png)

VuexもまたFluxの概念を継承した公式ライブラリで、Vuexのリアクティブを有効的に利用できるよう開発されました。
Vuexを理解するには3つの概念を理解すれば十分です

- State
- Mutation
- Action

## State
アプリケーションのSingle source of truthとなるようなオブジェクト。後述のMutationによってStateを変更する時に変更前後のスナップショットを撮れる

## Mutation
Vuexの状態を変更できる唯一の仕組み。Mutationがcommitされることで初めてStateが変更される。同期的な変更のみを許容する

## Action
Mutationのcommitをコントロールするレイヤー。非同期的な処理の記述や、複数のMutationのcommitができる


# 実際にVuexを使ってみる
### 1. Storeを定義する
簡単なStateを備えたStoreを定義します
```
// store.js
import Vuex from 'vuex'

const store = new Vuex.Store({
  state: {
    userName: "Shin Ando",
    email: "hoge@example.com"
  }
})

export default store
```

### 2. VueとStoreを連携する
`Vue.use(Vuex)`を実行することでグローバルにVueインスタンスとStoreの連携が可能になります。また、ルートインスタンスでStoreを読み込むことで、子孫コンポーネント全てでStoreが使用できるようになります

```
// app.js
import Vue from 'vue/dist/vue.esm.js'
import Vuex from 'vuex'
import store from './store'

Vue.use(Vuex)

const vm = new Vue({
  el: "#app",
  store
})
```

### 3.StoreのStateを使用する
Vuexに備わった `mapState()`関数を利用することで、StoreのStateから必要なものだけをVueで使用できるようになります

```
// app.js
import { mapState } from 'vuex'

...
computed: {
  ...mapState({
    name: state => state.userName,
    "email" // state => state.emailと同じです
  })
}
```

```
// index.html
<div id="app">
  <div class="name">{{ name }}</div>
  <div class="email">{{ email }}</div>
</div>
```

### 4.Stateを変更するMutationを作成する
Mutationは関数として実装し、第一引数には `state`が、第二引数には呼び出し時に渡された引数( `payload`)が渡されます

```
// store.js
...
const store = Vuex.Store({
  state: {..............},
  mutations: {
    changeName (state, newName) {
      state.userName = newName
    },
    changeEmail (state, newEmail) {
      state.email = newEmail
    }
  }
})
```

### 5.MutationをCommitする
実際にアプリケーションからMutationをCommitしてみます。 `this.$store.commit()`からCommitすることも可能ですが、一般的には `mapMutations()`関数を利用します。

```
// app.js
import { mapMutations } from 'vuex'

...
methods: {
  ...mapMutations(["changeName", "changeEmail"])
  // this.changeName(name), this.changeEmail(email)が呼べるようになる
  // this.$store.commit('changeName', name), this.$store.commit('changeEmail', email)がそれぞれ呼ばれる
}
```

```
// index.html
<div id="app">
  <div class="name">{{ name }}</div>
  <div class="email">{{ email }}</div>
  <input @keyup.enter="changeName()" placeholder="enter new name here">
</div>
```

### 6.Actionを活用する
DEMO(実例で紹介)
