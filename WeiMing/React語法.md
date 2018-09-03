# ES6基礎語法

- 變數宣告
在ES6中，新增了 let 和 const 兩個用來宣告的關鍵字。
let用法類似var，一樣用來宣告變數，但是它只在let宣告所在的範疇中有效。
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