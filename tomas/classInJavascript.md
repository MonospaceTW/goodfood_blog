# Class 繼承

`Javascript` 幾乎都是物件(null, undefined, NaN... 不是)

所以在 `Javascript` 並沒有 class 的存在

但是要如何實作繼承呢？

## Prototype

ES5 中多了一個特別的屬性 `Prototype` 來實作繼承

但是在 class 中都會有一個 `建構子` 

`建構子` 是一個 function 他來實體化

而在 Js 中 function 也是一個物件

所以實作會是

```js
const Person = (name) => {
  this.name = name;
}

Person.prototype.getName = () => this.name;

const tomas = new Person('Tomas');

console.log(tomas.getName()); // Tomas
```

但是在 ES6 中多了實作 class

但是經過 `babel` 編譯之後還是會轉成 ES5

上面的範例在 ES6 會是

```js
class Person {
  constructor(name) {
    this.name = name;
  }

  getName(){
    return this.name;
  }
}

const tomas = new Person('Tomas');

console.log(tomas.getName()); // Tomas
```

## Static and Public and Private

class 的 `property` 有三種類型

預設是 public

例如上述範例中的 `Person` 中的 name

```js
class Person {
  constructor(name) {
    this.name = name;
  }

  getName(){
    return this.name;
  }
}

const tomas = new Person('Tomas');

console.log(tomas.getName()); // Tomas
console.log(tomas.name); // Tomas
```

public 的參數只要知道名稱都可以直接取得

但是在 Javascript 中並沒有真正實作 private

而在 ES5 中會利用 `function scope` 的特性來實作 private

```js
function Person(name) {
  const _name = name;
  this.getName = () => name;
}

const tomas = new Person('Tomas');
console.log(tomas.getName()); //Tomas
console.log(tomas.name); // undefined
console.log(tomas._name); //undefined
``` 

上述例子中除了 getName 之外並沒有其他任何管道可以取得 name

這也可以模擬 private 保護某些特殊敏感資訊

在 ES6 中會是

```js
class Person {
  constructor(name) {
    const _name = name;
    this.getName = () => _name;
    //this.getName = () => name;
  }
}

const tomas = new Person('Tomas');
console.log(tomas.getName()); //Tomas
console.log(tomas.name); // undefined
console.log(tomas._name); //undefined
```

static 就是靜態的變數或是函式

主要在於僅僅留存於原本的 class

而不會透過繼承來傳遞該變數 (函式)

```js
function Person (name){
  this._name = name;
}

Person.staticName = 'static name';

const tomas = new Person('Tomas');

console.log(tomas.staticName); //undefined
console.log(Person.staticName); //Tomas
```

雖然我們有宣告了 Person 的 staticName 

但是在 tomas 並沒有 staticName

因為 static 不會被繼承

在 ES6 中的靜態參數如下

```js
class Person {
  constructor(name){
    this._name = name
  }
  static hello = "hello"
}

const tomas = new Person('Tomas');

console.log(tomas.hello); //undefined
console.log(Person.hello); // hello
```

為什麼要使用繼承？

我們在這個範例中宣告一個 Dog 的 class

裡面增加一些參數來描述狗的共通特性

一個 call 的 function  來描述狗的叫聲

我們宣告一個新的 blackDog 來繼承 Dog

另外也宣告一個新的 whiteDog 也是繼承 Dog

```js
class Dog {
  constructor({color, foot}){
    this.skinColor = color;
    this.hasFootCount = foot;
  }

  call() {
    return "汪!";
  }
}

class BlackDog extends Dog {
  constructor({foot}) {
    super({color: "black", foot});
  }
}

class WhiteDog extends Dog {
  constructor({foot}) {
    super({color: "white", foot});
  }
}

const smallBlack = new BlackDog({foot: 4});
const smallWhite = new WhiteDog({foot: 4});

console.log(smallBlack.skinColor); //black
console.log(smallBlack.hasFootCount); // 4
console.log(smallBlack.call()); // 汪

console.log(smallWhite.skinColor); //white
console.log(smallWhite.hasFootCount); // 4
console.log(smallWhite.call()); // 汪
```

小黑與小白都有 `Dog` 的特性 (skinColor, foot)

也都會叫, 聲音也都是 `汪`

但是在 `smallBlack` 和 `smallWhite` 中都沒有這些設定

那這些設定是從哪裡來的呢？

這是特別的地方

因為 `smallBlack` 和 `smallWhite` 的 constructor 都有 `super`

這在這個範例中代表了 `Dog` 的 class

因為 `smallBlack` 和 `smallWhite` 都繼承 (extends) 了 Dog

我們各自傳入了 color 和 foot

由 Dog 來初始化 `skinColor` 和 `foot` 的參數

而且也可以覆蓋掉 Dog 的 method

```js
class Dog {
  constructor({color, foot}){
    this.skinColor = color;
    this.hasFootCount = foot;
  }

  call() {
    return "汪!";
  }
}

class BlackDog extends Dog {
  constructor({foot}) {
    super({color: "black", foot});
  }

  call(){
    return "汪！汪！"
  }
}

class WhiteDog extends Dog {
  constructor({foot}) {
    super({color: "white", foot});
  }
}

const smallBlack = new BlackDog({foot: 4});
const smallWhite = new WhiteDog({foot: 4});

console.log(smallBlack.call()); //汪！汪！
console.log(smallWhite.call()); //汪!
```

在 `smallBlack` 中覆寫了 call

所以在 `smallBlack.call()` 被覆蓋 而不是原本的 Dog.call

另外也有一種情境

需要先執行 parent 的 method 之後再做一些處理

這種 Desing pattern 叫做 `proxy`

實作可以利用 `super` 來實踐

```js
class Dog {
  constructor({color, foot}){
    this.skinColor = color;
    this.hasFootCount = foot;
  }

  call() {
    return "汪!";
  }
}

class BlackDog extends Dog {
  constructor({foot}) {
    super({color: "black", foot});
  }

  call(){
    const parentCallResult = super.call();
    return `${parentCallResult}在這偷加料!`;
  }
}

class WhiteDog extends Dog {
  constructor({foot}) {
    super({color: "white", foot});
  }
}

const smallBlack = new BlackDog({foot: 4});
const smallWhite = new WhiteDog({foot: 4});

console.log(smallBlack.call()); //汪!在這偷加料!
console.log(smallWhite.call()); //汪!
```

這些都是對 ES6 基本的介紹

其實有蠻大量的應用

就像是 React 中的 propTypes 就是 static 的應用

因為他只是一個 type checking 

所以他在實體化的時候不需要存在的情境

