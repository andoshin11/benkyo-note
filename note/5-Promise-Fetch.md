# 本日のゴール
- 非同期処理の概念を理解する
- 「 `Promise`を返す」という言葉を理解する
- 「 `Promise`を解決する」という言葉を理解する(resolve, reject)
- `async/await`を使えるようになる
- `fetch`の使い方を覚える

# JavaScriptの同期処理
Webブラウザで動くJavaScriptは、基本的にコードを上から1行ずつ実行していきます。例えばある関数を実行するとその関数が終了するまで( `return`文が実行されるまで)、それ以降のコードは実行されません。

この弊害として、仮に時間のかかる処理を実行すると、その間はマウスクリックイベントを検知できない、DOMの変更を検知できない、その他のイベントハンドラが実行されない等のブロッキングが発生してしまいます。

以下の例は「実行すると5秒後に結果を表示し、さらに3秒後に別の結果を表示するタイマー」の例です。最悪な実装なので真似しないように。

```javascript
var oldDate = new Date();
console.log('タイマー開始);

// 5秒経つまで無限ループ
while((new Date() - oldDate) < 5000);

console.log('5秒経ちました');

oldDate = new Date();

// 3秒経つまで無限ループ
while((new Date() - oldDate) < 3000);

console.log('さらに3秒たちました');

```

Web APIを呼び出すためにサーバーとの通信が頻繁に発生するようなモダンアプリケーションの場合、同期的なJavaScriptの挙動をそのまま採用することは致命的なストレスをユーザーに与えることに繋がります。

# 非同期処理の登場
「コードは上から順番に実行されてほしい」「でも時間がかかる処理で止まって欲しくない」という課題を解決するために、

**「ある関数を呼び出すとき、戻り値として本来渡したい結果を返すのではなく、一度関数としては終了し（＝呼び出し元に戻る）、後で『本来渡したかった値』を返せる状態になったときに、呼び出し元にその値を通知する」**

という非同期処理の仕組みが考え出されました。
これによりレスポンスを受け取るまで時間のかかるWeb APIをfetchしている間も、ブラウザでイベントハンドリングなどを行うことが可能になったのです。

# setTimeoutの登場
流石に `while`文を回しっぱなしにするのはよくないということで登場したのが `setTimeout`関数です。

`setTimeout`関数は第一引数にコールバック関数を、第二引数に処理を呼び出す回数をミリ秒で指定できます。上記のタイマー処理を `setTimeout`に置き換えてみましょう

```javascript
console.log('タイマー開始');

setTimeout(function(){
  console.log('5秒経ちました');

  setTimeout(function(){
    console.log('さらに3秒経ちました');
  }, 3000);
}, 5000);
```

先ほどの `while`関数を無限ループさせるパターンと違い、 `setTimeout`を利用することで処理のブロッキングを発生させることなく、遅延処理を行うことができました。

# setTimeoutによるネスト地獄
非同期処理を実装するのに便利なように思える `setTimeout`関数ですが、ある大きな問題を抱えていました。

それは非同期で実行したい処理の数だけ、ネストが深くなってしまうことです。
これはコードの可読性を著しく下げるだけでなく、コード全体の保守性を低下させる深刻な問題です。

ここからは実際にAjax通信を用いてWebサーバーと通信する例を考えてみます。まずはダメな例から

```javascript 
var URL = "getData.php";
var count = 0;
var dataList = [];

// 無限ループでajax通信を行う
while(true){
    $.ajax({
        type: "POST",
        url: URL,
        dataType: "text",
        param: { count: count },
        success: function(data){
            // データが空っぽだったら終了するために無限ループから抜ける
            if(data === ""){
                break;
            }

            // データが入っていたら、dataListに内容を追加する
            dataList.push(data);
        }
    });

    // 次のajaxのために値を1足す
    count++;
}
```

これはあるエンドポイントにPOSTリクエストを送り続けて、帰って来るデータがなくなったら `break`するという処理ですが、当然ループの実行中にスレッドを占有するためアウトです。というかもういろいろとアウト。

次は `setTimeout`のようにコールバックを指定して、非同期にリクエストを呼び出す方法で実装します。

```javascript
var URL = "getData.php";

$.ajax({
    type: "POST",
    url: URL,
    dataType: "text",
    param: { count: 0 }, // 1回目はパラメータとしてcountに0を与える
    success: function(data1){
        // データが空っぽだったら終了するためにコールバックを呼ぶ
        if(data === ""){
            callback([]); // 前回までのデータを全て入れておく(初回なので空)
            return;
        }
        $.ajax({
            type: "POST",
            url: URL,
            dataType: "text",
            param: { count: 1 }, // 2回目はパラメータとしてcountに1を与える
            success: function(data2){
                // データが空っぽだったら終了するためにコールバックを呼ぶ
                if(data === ""){
                    callback([data1]); // 前回までのデータを全て入れておく
                    return;
                }
                $.ajax({
                    type: "POST",
                    url: URL,
                    dataType: "text",
                    param: { count: 2 }, // 3回目はパラメータとしてcountに2を与える
                    success: function(data3){
                        // 以下省略
                        // あと114514回ネストを書く
                    }
                })
            }
        })
    }
});
```

一応これでスレッドを占有することなく、非同期な処理を記述することができました。ですが見てわかるように、この書き方をするとネスト地獄で死にます。

処理全体を再帰的に記述することもできますが、本質的な問題を解決していないことに変わりはありません。

# Promise
ES6以降のモダンJSにおいて最も重要といっても過言ではないのが `Promise`パターンです。
`Promise`パターンの基本的な考え方としては、

**「関数が呼び出されたら本来の値ではなくPromiseオブジェクトを呼び出しもとに返す。期待する処理が完了したらPromiseオブジェクトを通して呼び出しもとに値を渡す」**

という流れです。

実際に `Promise`オブジェクトが作成されるところを見てみましょう

```js
const fetchSomething = () => {
  return new Promise((resolve, reject) => {

  })
}
```

`Promise`オブジェクトを `new`する際は引数に関数を定義します。

### resolveとreject
`Promise`の引数である関数では `resolve`と `reject`という2つの値を指定できます。 `resolve`と `reject`は `Promise`がデフォルトで持つ機能であり、どちらも実態はメソッドです。
(* `reject`については省略可能ですが、 `resolve`は必ず必要です)

`Promise`オブジェクトには以下の
- `pending`: 未解決
- `fulfilled`: 完了
- `rejected`: 棄却

という3つの状態があります。

![Promiseの状態](https://html5experts.jp/wp-content/uploads/2015/09/states.png)

1つの `Promise`オブジェクトにおける状態遷移は一方通行であり、一度状態が変化するとそれ以降変わることはありません。

`Promise`オブジェクトが持つ `resolve`, `reject`メソッドとはこの状態を変化させるためのトリガーです。
はじめに `Promise`オブジェクトが作成される際は `pending`の状態で呼出しもとにオブジェクトが返されますが、 `Promise`の内部から任意のタイミング(Web APIの取得が完了した際など)で `resolve`関数を実行することによりオブジェクトの状態が `fulfilled`となり、以下に説明する `.then()`で `Promise`とチェーンされた処理を同期的に実行することが可能になります

反対に `reject`メソッドを実行することは処理が失敗したことを指し、 `.catch`メソッドに値が受け渡され実行されます。


### Promiseの使い方

`Promise`を仕様するパターン

```js
function readFileAsync(file) {
  return new Promise(function(resolve, reject){
    fs.readFile(file, function(err, data){
      if (err) {
        reject(err); // errがあればrejectを呼び出す
        return;
      }

      resolve(data); // errがなければ成功とみなしresolveを呼び出す
    });
  });
}
```

### エラーハンドリング
先に `Promise`オブジェクトを返していても処理が失敗することは起こり得ます(例：サーバーが404を返すなど)。

### Promise.all()
`Promise.all()`は引数に `Promise`オブジェクトの配列を受け取り、全ての `Promise`オブジェクトが `resolve`されたら `then`を実行します。

```js
const sayHi = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('Hi');
    resolve();
  }, 1500);
});

const sayHey = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('Hey');
    resolve();
  }, 1000);
});

const before = new Date();
Promise.all([taskA, taskB]).then(() => {
  const after = new Date();
  const result = after.getTime() - before.getTime();
  console.log(result);
});

```

先ほど `setTimeout`による非同期処理を `Promise`で書き換えて見ましょう。

```javascript
const URL = "getData.php";
let dataList = [];
let count = 0;

const fetchData = () => {
  // Promiseオブジェクトを返す関数
  return new Promise((resolve, reject) => {
    $.ajax({
      type: "POST",
      url: URL,
      dataType: "text",
      param: { count: count },
      success: (data, resolve, reject) => {
        if (data) {
          dataList.push(data);
          resolve();
        } else {
          reject();
        }
      }
    })
  })
};

fetchData()
  .then(fetchData(result))
  .then(fetchData(result))
  .catch(e => alert(e));
```

# Promiseの抱える問題
人類は `Promise`オブジェクトを獲得することで、「任意のタイミングで」「任意の非同期処理を」を呼び出せるようになりました。

しかし `Promise`もまた、その特殊な記述方法ゆえに一定の問題を孕んでいます。

例えば前述のように「空データが返ってくるまで無限にajaxを繰り返す」という処理を書く際、Promise内でPromise自身を再帰的に呼ぼうとすると、一気に可読性の低いコードになってしまうのです

# async/awaitの発明
上記のような複雑な `Promise`パターンは `async/await`を用いることで簡単に記述することができます。 `async/await`は `ES7`から新しく持ち込まれた概念です。

`async`とは、その関数が非同期関数であることを宣言するための関数宣言です。

### 非同期関数(async関数)は何をするか
非同期関数(async関数)は通常の関数と違い、以下のような特徴があります。

- 非同期関数は呼び出されると、 `Promise`を返す
- 非同期関数内で `return`が実行されると、 `Promise`が戻り値を `resolve`する
- 非同期関数内でエラーを `throw`すると、 `Promise`がその値を `reject`する

これにより `Promise`の記法を用いずに、簡単に `Promise`パターンを利用することができますね。

以下が `async`関数の使用例です。

```jacascript
// resolve1!!をreturnしているため、この値がresolveされる
async function resolveSample() {
    return 'resolve!!';
}

// resolveSampleがPromiseを返し、resolve!!がresolveされるため
// then()が実行されコンソールにresolve!!が表示される
resolveSample().then(value => {
    console.log(value); // => resolve!!
});
```

# awaitの使い方
`await`は非同期関数の中で使える機能で、非同期関数内で `Promise`が呼ばれた際に `resolve`もしくは `reject`が返ってくるまで待つ(一時停止する)機能です。

実際の利用例を見てみましょう

```javascript
// 実行された2秒後に `resolve`する `Promise`
function sampleResolve(value) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(value * 2);
        }, 2000);
    })
}

// 'result + 5'をresolve()する非同期関数
async function sample() {
    const result = await sampleResolve(5);
    return result + 5;
}

sample().then(result => {
  console.log(result); // 15
})

```

上記の例では非同期関数である `sample()`の中で `Promise`を返す関数の `sampleResolve()`が呼ばれています。

`const result = await sampleResolve(5);`

の行と

`return result + 5;`

は `Promise.then()`で結ばれているわけではない普通の記述方法なので、 `await`を付けずに実行すると `result`には `Pending`状態の `Promise`が代入されてしまいます。
ですが `await`を利用することで `sampleResolve()`が `resolve()`されるまで処理を一時停止することができるようになり、

`return result + 5;`

が実行されるタイミングで確実に `10 + 5`という処理が行われます。
ちなみに上記の処理を `Promise`で書くと以下のようになります。

```javascript
function sampleResolve(value) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(value * 2);
        }, 2000);
    })
}

function sample() {
    return sampleResolve(5).then(result => {
        return result + 5;
    });
}

sample().then(result => {
    console.log(result); // => 15
});
```

上記のシンプルな例を見るだけでも
- `sample()`が `Promise`を返す関数であることがわかりにくい
- 無駄にネストが増える

といった理由から `async/await`を使った記述方法の方が優れていることがわかりますね

# Fetch概要
WebサーバーとAjax通信する方法としては `$.ajax`が一般的ですが、流石にそのためだけに `jQuery`は入れたくないところです。かといって `XMLHttpRequest`を利用するのも流石にやりたくないです。

そこで登場したのが `fetch`という規格です。
`fetch()`は第一引数に対象のURLを受け、 `Promise`を返すメソッドです。

```javascript
fetch('https://sample.com'); // Promiseを返す
```

`fetch()`は通信に成功すると `Response`オブジェクトを返しますが、
