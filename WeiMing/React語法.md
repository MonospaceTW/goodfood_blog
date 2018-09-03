# ES6基礎語法

### 變數宣告
在ES6中，新增了 let 和 const 兩個用來宣告的關鍵字。
let用法類似var，一樣用來宣告變數，但是它只在let宣告所在的範圍中有效。
```jsx
{
  var foo = 'Hello'
  let bar = 'World'
}

console.log(foo);   //Hello
console.log(bar);   //bar is not defined
```
```jsx
{
  var foo = 'Hello'
  let bar = 'World'
  console.log(foo);   //Hello
  console.log(bar);   //World
}
```
在使用var時


### 展開運算值 spread
在ES6之前，我們若要將一個陣列中的所有值，放入另一個陣列或是當作函數的參數，我們通常需要將陣列中值用[迭代](https://zh.wikipedia.org/wiki/%E8%BF%AD%E4%BB%A3)的方式處理，或是一個一個列出。
在ES6中，我們可以使用展開運算子(spread)，而它的使用方法是在陣列前面加上三個點(...)，就可以將該陣列中的值展開。
```jsx
const foo = [1,2,3]
const bar = [...foo,4,5]
console.log(bar); // [1,2,3,4,5]
console.log(...bar); // 1 2 3 4 5
```

這種方法不只可用在值上面，也可以用在物件上面。
```jsx
const foo = {
  a: 1,
  b: 2,
  c: 3,
}
const bar = {
  ...foo,
  d: 4,
  f: 5,
}
console.log(bar); // {a: 1, b: 2, c: 3, d: 4, f: 5}
```