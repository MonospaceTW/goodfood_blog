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

這個時間差大約 496 ms

因為 `step1` 和 `otherWork` 的 `setTimeout` 差距了 `500ms`

會造成這樣的結果

## 未完待續