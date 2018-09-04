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


### 定義函數
很簡單，只要這樣
```jsx
//ES5
var foo = function(){
  console.log('Hello');
};

//ES6
let foo = () => {
  console.log('Hello');
}
```
簡單來說，就只是把function替換成 =>

如果函數只需要回傳一條陳述句或是運算式的結果時，可省略大括號。
```jsx
//ES5
var foo = function (num1, num2) {
  return num1 + num2
};

//ES6
let foo = (num1, num2) => num1 + num2
```

函數中只有一個參數，可省略小括號。
```jsx
//ES5
var foo = function(){
  return num * 2
};

//ES6
let foo = num => num*2
```

箭頭符號還有一個很重要的特性，那就是它所定義的函數中的 this ，會使用外面一層的 this ，也因為這個特性，我們可以用它來定義某些需要外面一層 this 的回呼函數。
```jsx
//ES5
var obj = {
  foo: 'Hello',
  greet: function () {
    var self = this;
    setTimeout(function () {
      console.log(self.foo);
    },1000);
  }
}
obj.greet(); //Hello

//ES6
let obj = {
  foo: 'Hello',
  greet: function () {
    setTimeout(() => {
      console.log(this.foo);
    },1000);
  }
}
obj.greet(); //Hello
```

### 閉包 Closure
在JS中中，函數擁有著自己的獨立義域(Domain)，可以宣搞自己的變數在函式裡，且忽略外部相同名稱的變數 / 不被外部相同名稱的變數引響。
這樣的情況稱之為閉包 Closure
```jsx
var x= 50;

function closureFunc() {
  var x = 100;
  return x;
}
console.log(x); //50
console.log(closureFunc()); //100
```
