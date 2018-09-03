## Life cycle 組件
- 如何設計組件？
`繼承React.Component類別，並且實作render方法，必要的時候會使用自己的建構式。`
- 有組件後的重要操作
1. 建立組件，系統會依照下列順序執行
```
  1. constructor(); 系統先呼叫建構式，建構式執行完畢，呼叫componentWillMount

  2. componentWillMount(); 表示react正要把這組件繪製到畫面上，接著呼叫render

  3. render(); 把組件中的邏輯寫進去，然後畫到畫面上，執行componentDidMount

  4. componentDidMount(); 3畫到畫面上之後，確定組件已掛載到畫面上，確定後做一些動作
```

2. 更新組件 => 更新組件的狀態（畫面）

```
  1. componentWillUpdate(); 更新前的預備動作
  2. render(); 更新（重新繪製）
  3. componentDidUpdate(); 確定更新完成
```

3. 刪除組件
```
1. componentWillUnmount(); 刪除組件前先呼叫這行，組件要被刪除之前讓組有機會做一些工作
```


**以上這些方法都是定義在React.Component（父類別）裡的**
**所以我們都是可以做複寫動作**
**如果有要更新這些組件的邏輯就複寫**

## [React v16.3 新生命周期](https://juejin.im/post/5aca20c96fb9a028d700e1ce)
由於 React 出了新的生命週期 v16.3 ，所以之前所學的生命週期都白費了，那我們就來看看有什麼不一樣吧！屆

- 把下面三個組件都去掉
```jsx
componentWillMount

componentWillReceiveProps

componentWillUpdate
```
- 為了彌補這三個組件所推出的是：
```jsx
staticgetDerivedStateFromProps

getSnapshotBeforeUpdate
```
**別擔心，那三個舊的在 v16 版本還是可以使用的**

...Coming Soon