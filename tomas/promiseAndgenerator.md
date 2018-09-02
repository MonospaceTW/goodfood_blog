# Promise

[相關來源](https://eyesofkids.gitbooks.io/javascript-start-es6-promise/content/)

Promise 解決 callback hell 的問題

這部分應該很多人都了解

```js
const step1 = () => setTimeout(() => console.log(1), 2000);
const step2 = () => setTimeout(() => console.log(2), 1000);
const step3 = () => setTimeout(() => console.log(3), 3000);
step1();
step2();
step3();
```

如果我們希望執行之後可以依照順序 顯示 1, 2, 3

但是結果卻是 2, 1, 3

這時候我們要做一些調整

```js
const step1 = (cb) => setTimeout(() => {
  console.log(1);
  cb();
}, 2000);
const step2 = (cb) => setTimeout(() => {
  console.log(2);
  cb();
}, 1000);
const step3 = (cb) => setTimeout(() => {
  console.log(3);
  cb();
}, 3000);

step1(() => 
  step2(() => 
    step3(() => 
      console.log('done'))));
```

這樣依序將 function 當作參數傳入下一個 `function` 作為他的 `callback`

在執行完之後再執行 `callback` 確保他的順序性

這樣一層一層往下疊加 `callback` 會造成 `callback hell` 的問題

我們可以利用 `Promise` 攤平化

```js
const step1 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(1);
    resolve();
  }, 2000);
});

const step2 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(2);
    resolve();
  }, 1000);
});

const step3 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(3);
    console.log('done');
    resolve();
  }, 3000);
});

step1()
  .then(step2)
  .then(step3);
```

在語義上可以有更清楚地描述

## reolsve and reject

在 `Promise` 中預設傳入兩個 `function`

第一個預設為 `resolve`

第二個預設為 `reject`

這個 function name 是可以任意修改的

只是約定成俗會命名為 `resolve` 和 `reject`

但是其實 `Promise` 解決的不只是這個問題

另外也解決了工作流程的問題

```js
const step1 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(1);
    resolve();
  }, 2000);
});

const step2 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(2);
    resolve();
  }, 1000);
});

const step3 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(3);
    console.log('done');
    resolve();
  }, 3000);
});

step1()
.then(step2)
.then(step3);

```

可以藉由 `then` 去限制你的工作流程

強制使工作有一個順序性

但是又不會 `block` 住

```js
let step1WrokStart = null,
  otherWorkStart = null;


const step1 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(1);
    step1WrokStart = new Date();
    resolve();
  }, 2000);
});

const step2 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(2);
    resolve();
  }, 1000);
});

const step3 = () => new Promise(resolve => {
  setTimeout(() => {
    console.log(3, otherWorkStart - step1WrokStart);
    console.log('done');
    resolve();
  }, 3000);
});

const otherWork = () => new Promise(resolve => {
  setTimeout(() => {
    console.log('other work');
    otherWorkStart = new Date();
    resolve();
  }, 2500);
});

step1()
.then(step2)
.then(step3);

otherWork();
```

上述範例的  console 依序是

```
1
other work
2
3 496
done
```

也就是在 執行 之後不會完全等待 step1 2 3 完全執行完畢

而中間先執行 `otherWork`

和 `step1` 時間差大約 496 ms

因為 `step1` 和 `otherWork` 的 `setTimeout` 差距了 `500ms`

會造成這樣的結果

為什麼要這樣讓他們依序執行呢？

通常最常見的就是彼此之間會有 `相關性`

例如

```js
const mockFetch1 = () => new Promise(resolve => {
  setTimeout(() => resolve(10), 1000);
});

const mockFetch2 = (mockFetch1Resp) => new Promise(resolve => {
  setTimeout(() => resolve(fetch1Resp + 5), 1000);
});

mockFetch1()
.then(mockFetch2)
.then(console.log)
// 15
```

因為在 `mockFetch2` 中需要 `mockFetch1` 的 response

所以他必須要等到 `mockFetch1` 執行完之後 在執行

這是最簡單的流程控制

但是並不是每一次都只能完成一件事情

`Javascript` 的優點就是在於非同步

可以有效的提高效率

```js
const mockFetch1 = () => new Promise(resolve => {
  setTimeout(() => resolve(10), 1000);
});

const mockFetch2 = ([fetch1Resp, fetch3Resp]) => new Promise(resolve => {
  setTimeout(() => resolve(fetch1Resp + fetch3Resp), 1000);
});

const mockFetch3 = () => new Promise(resolve => {
  setTimeout(() => resolve(2), 1500);
});

Promise
  .all([mockFetch1(), mockFetch3()]) //input 是陣列
  .then(mockFetch2) //output 也是陣列順序對應
  .then(console.log); // 10 + 2 = 12

```

會同時發送 `mockFetch1` 和 `mockFetch3`

在 `mockFetch2` 用陣列對接到 `mockFetch1` 和 `mockFetch3` 的 response

可以做併發之後再聚合

這是簡單的多個 `Promise` 流程控制

## Generator

`Generator`
## 未完待續