# What is Vue.js?
Vue.js(ビュージェイエス)とは、オープンソースのJavaScriptフロントエンドフレームワーク。生みの親であるEvan YouはGoogle出身で元々Angular.jsを使ってた。AngularとReactのいいとこ取りをして2014年リリース。v2.0.0から爆発的に性能が向上し認知度も急上昇。
Laravelで正式採用。WordPressの次世代標準最有力候補。

2016年のGitHubスター数第3位！
1位：Free Code Camp
2位：google-interview-university
3位：**Vue.js**
4位：Tensorflow
5位：free-programming-books
6位：**React**

# Major Features
- Virtual DOM
- リアクティブ
- HTMLベースのテンプレート
- コンポーネント
- トランジション

**Virtual DOM**
旧来のJavaScript:
DOMに変更を加えたい時は、セレクタで対象をHTMLから抽出→操作

Virtual DOM:
HTMLの構造をメモリ上の仮想空間に持ち、その状態をJavaScriptで管理。
JSの状態に合わせたDOMがHTML上に出力される。← HTMLで条件分岐やリストレンダリングなど、なんでもできる！

# Quick Look Back
|Date|Versions|Name|Features|
| --- |---  |---  |---  |
| 2014/2/25 |0.9.0  |Animatrix  |  |
| 2014/3/23 |0.10.0  | Blade Runner |  |
| 2014/11/7 |0.11.0  |Cowboy Bebop  |  |
| 2015/6/12 | 0.12.0 | Dragon Ball |  |
| 2015/10/27 |1.0.0  | Evangelion |  |
| 2016/9/30 | 2.0.0 |Ghost in the Shell  | Virtual DOM Improvement, JSX Support,  |
| 2016/11/22 |2.1.0  | Hunter X Hunter |Scoped Slots, v-else-if  |
| 2017/2/26 |2.2.0  |Initial D  |  |
| 2017/4/27 |2.3.0| JoJo's Bizarre Adventure |  | 
| 2017/7/13 |2.4.0  |Kill la Kill  |SSR, Async Component, Weex|
|2017/10/13|2.5.0|Level E|Error capture, TypeScript support|

https://github.com/vuejs/vue/releases

投票は[こちら](https://github.com/egoist/always-bet-on-vue/issues/5)


# I'm loven' it!!
- 軽い！(Angular: 144KB, React: 142KB, Vue: 26KB)
- 速い！
- コンポーネント最高
- シンタックスが直感的
- 書いてて楽しい！！(大事)


# Comparison with React
https://medium.com/js-dojo/react-or-vue-which-javascript-ui-library-should-you-be-using-543a383608d
### Common Features
- 仮装DOM
- コンポーネント指向(スコープがよく似ている)
- リアクティブ
- vue-router
- Flux(Vuex / Redux)
- Native App(Weex / React Native)

### What's so special about?
- Reactは状態に変更が加わった際、該当コンポーネント以下のサブツリー全体を再描画する。再描画不要なコンポーネントには明示的に `shouldComponentUpdate `を与えなければいけない。
- Vueではコンポーネントの依存関係が自動で追跡されるため、必要なものだけ再描画される
- JSX is lame AF
- VueのシンタックスはHTMLの拡張。学習コストが低く、可読性が圧倒的に高い
- ***ES5で書ける！！***
- ***[日本語のドキュメントが豊富](https://jp.vuejs.org/v2/guide/)***

# Speed Comparison
||angular v4.4.3|react v15.5.4|vue v2.4.4|
|---|--- |--- |---  | 
| createing 1,000 rows |187.11 |229.74| 172.73 |
| clearing 10,000 rows |373.28 |376.51  |248.15  |

https://rawgit.com/krausest/js-framework-benchmark/master/webdriver-ts/table.html

Rendering 10,000 items of list for 100 times
![](https://cdn-images-1.medium.com/max/1600/1*xy2KzzZrqP0mdLUbIdC-xA.png)

# Let's get started!
`index.html`を用意し、`<script>`にCDNから`Vue`を読み込む

```
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8">
    <title>Hello Vue</title>
  </head>
  <body>
    <div id="app">
      <p>{{ message }}</p>
    </div>
    <script src="https://unpkg.com/vue"></script>
    <script>
      var app = new Vue({
        el: "#app",
        data: {
          message: 'hello world'
        }
      })
    </script>
  </body>
</html>
```

# Basic Syntax
## Hello World
```index.html
<div id="app">
  {{ message }}
</div>
```
```app.js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
![](https://gyazo.com/ef6e00b1efa7e2067ef0cae7813efaf7.png)

## Use Date Object
```index.html
<div id="app">
  {{ message }}
</div>
```
```app.js
var app = new Vue({
  el: '#app',
  data: {
    message: 'The clock shows us ' + new Date().toLocaleString()
  }
})
```
![](https://gyazo.com/5cda6873ae7dce5c07c22089137ffb62.png)


## List Rendering
```index.html
<div id="app">
  <ul>
    <li v-for="member in members">
      {{ member.name }}
    </li>
  </ul>
</div>
```
```app.js
var app = new Vue({
  el: '#app',
  data: {
    members: [
      { sex: "male", name: "Shin Ando"},
      { sex: "male", name:  "Yukinori Nakamura" },
      { sex: "female", name: "Fukuda Momoko" }
    ]
  }
})
```
![](https://gyazo.com/2c24305ed02fef8d5fca38693ec7a667.png)


## Two Way Binding
```index.html
<div id="app">
  <div>I'm feeling {{ yourEmotion }}</div>
  <input type="text" v-model="yourEmotion">
</div>
```
```app.js
var app = new Vue({
  el: '#app',
  data: {
    yourEmotion: "normal"
  }
})
```
![](https://gyazo.com/71fe3148d600e365ecd1019f3562ae39.gif)


## Event Handling
```index.html
<div id="app">
  <div>Let's count {{ number }}</div>
  <button @click="increment()">increment number</button>
</div>
```
```app.js
var app = new Vue({
  el: '#app',
  data: {
    number: 1
  },
  methods: {
    increment () {
      this.number++
    }
  }
})
```

![gif](https://gyazo.com/791dcb340c167182b51512754a695ce2.gif)


## Conditional Rendering
```index.html
<div id="app">
  <div v-if="isVisible">Now you see me</div>
  <div v-else>Now you don't</div>
  <button @click="isVisible = !isVisible">Visible status: {{ isVisible }}</button>
</div>
```
```app.js
var app = new Vue({
  el: '#app',
  data: {
    isVisible: true
  }
})
```
![](https://gyazo.com/7f1dcd5e73840c5d0fdfadf13c07c44b.gif)


## DEMO
https://github.com/andoshin11/vue-todoList-sample

# How it works on Production
## Ruby on Rails
- `Rails` + `Browserify` + `Vueify`
- REST APIをサーバーサイドで実装(Devise Token Auth含む)
- `mounted`で `fetch`した情報をもとに描画
- テーブルのソート処理、チャートのリレンダリング、Inboxのリアルタイム検索、サービスチュートリアル等
- Controller準拠のコンポーネントマネジメント

```hogeIndex.vue
<template>
  <div class="container">
  ...
    <div class="col-md-9 col-xs-12">
      <div class="row hide-sm">
        <div class="dress col-md-4 col-xs-6" v-for="item in filteredItems">
          <favorite :itemId="item.id"/>
          <div class="dress__sizes">
            <div class="dress__sizes__size">6</div>
            <div class="dress__sizes__size">8</div>
            <div class="dress__sizes__size">10</div>
          </div>
          <div class="dress__images">
            <img :src="image.url" alt="" class="dress__images__image" :class="[ image.category == 1 ? 'main' : 'sub' ]" v-for="image in item.images">
          </div>
          <a :href="itemLink(item)" class="dress__designer is-bold">{{ item.designer.name }}</a>
          <a :href="itemLink(item)" class="dress__name">{{ item.name }}</a>
          <div class="dress__price is-bold">
            <span class="dress__price__hire">Hire £ {{ item.hirePrice }}</span>
            <span class="dress__price__retail">Hire £ {{ item.retailPrice }}</span>
          </div>
        </div>
      </div>
    ...
    </div>
  </div>
</template>


<script>
  import indexField from './index_field';

  export default indexField;
</script>

```

```hogeIndex.js
import HTTP from './../http';
import datePicker from './date_picker.vue';
import favorite from './../vue/favorite.vue';
import vueSlider from 'vue-slider-component/src/vue2-slider.vue';

export default {
  components: {
    datePicker,
    favorite,
    vueSlider,
  },
  computed: {
    filteredItems() {
      let items = this.items;
      return items.filter(x => this.priceRange[0] <= x.hirePrice).filter(x => x.hirePrice <= this.priceRange[1]);
    },
    pageTotalNum() {
      return Math.floor( this.filteredItems.length / this.viewNum ) + 1;
    },
    pages() {
      let pages = [];
      for (let i = 1; i <= this.pageTotalNum; i++) {
        pages.push(i);
      }
      return pages;
    }
  },
  data() {
    return {
      priceRange: [0, 500],
      viewNum: 14,
      currentPage: 1,
      items: [],
      sortBy: [
        {
          label: "FEATURED",
          key: 0,
        },
        {
          label: "PRICE HIGH TO LOW",
          key: 1,
        },
        {
          label: "PRICE LOW TO HIGH",
          key: 2,
        },
        {
          label: "MOST POPULAR",
          key: 3,
        },
      ],
      sortByKey: 0,
    };
  },
  methods: {
    async fetch() {
      try {
        const { items: items } = await HTTP.get('/api/items');
        this.items = items;
      } catch (e) {
        console.error(e);
      }
    },
    toggleActive(className) {
      const target = document.getElementsByClassName(className)[0];
      this.removeActive(target);
      target.classList.toggle('active');
    },
    removeActive(exception) {
      const active = document.getElementsByClassName("active");
      if (!active) return;
      Array.from(active).forEach(x => {
        if(x != exception) x.classList.remove('active');
      })
    },
    itemLink(item) {
      return `/items/${item.id}`
    }
  },
  mounted() {
    this.fetch();
  },
};
```

## WordPress
- scriptタグで直接読み込んで、 `hoge-single.php`にES5記述
- 最新のWordPressは標準でREST APIが実装されてるので、ページがマウントされるたびに `fetch`

## Django
- `Django` + `Webpack` + `vue-route`
- Django REST Frameworkを利用してToken Base Auth
- `View`準拠のコンポーネントマネジメント
- 試行錯誤中

## SPA(Vue only)
- バックエンドにFirebase(NoSQL)
- `Webpack` + `Vue` + `vue-router` + `Vuex`
- アプリケーションの状態は `Vuex`で一限管理し、ページが変わっても持ち越せるようにする
- FirebaseへのリファレンスをViewに組み込むことで、クラウド上のDBの変更がリアルタイムで反映される
- Two Way Bindingできるライブラリがなかったので、自分でアダプターもどきを書いて、変更は `Vuex`の `Actions`のみで行えるようにする(影響範囲が小さい)
