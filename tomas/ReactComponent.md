# React

## Component

React 是一個 `Component` 的世界

也是為了 [SPA(Single Page Application)](https://zh.wikipedia.org/wiki/%E5%8D%95%E9%A1%B5%E5%BA%94%E7%94%A8) 而設計的一個 Lib

因為在 React 中每一個元件都可以是一個單位 `Component`

所以如何切割 `Component`

如何 Grouping `Component` 在 React 中是非常重要的一個課題

## Grouping Component

React 中其實沒有實際頁面的觀念

每一個頁面也是一個 `Component` 只是這個 `Component` 是許多小的 `Component` 的組合

我們用 [goodfood_admin](https://github.com/MonospaceTW/goodfood_admin/blob/master/src/App.js)來做說明

```javascript
import React, { Component } from 'react';
import LoginScene from './components/LoginScene';
import MainScene from './MainScene';
import {fetchPOST} from './apis/libs/fetch';
import 'semantic-ui-css/semantic.min.css';

class App extends Component {
  constructor(props) {
    super(props)
    this.state = {
      auth: {
        isLogin: false,
        message: ''
      },
      routes: {
        currentPage: 'orderDetail'
      }
    };
  }

  setLoginState = (updatedAuthData) =>
    this.setState({auth: {...this.state.auth, ...updatedAuthData}});

  changeCurrentPage = (currentPage) =>
    this.setState({routes: {currentPage}});

  handleLogin = async (payload) => {
    const {success} = await fetchPOST('/login', payload);
    
    const updatedData = success ? {isLogin: true} : {message: '登入失敗'};

    this.setLoginState(updatedData);
  }

  MainScene = () =>
    <MainScene
      currentPage={this.state.routes.currentPage}
      changePage={this.changeCurrentPage}
      logout={() => this.setLoginState({isLogin: false})} />
  
  LoginScene = () =>
    <LoginScene handleLogin={this.handleLogin}/>

  render() {
    return this.state.auth.isLogin
      ? this.MainScene()
      : this.LoginScene();
  }
}

export default App;
```

`App` 是 `goodfood_admin` 中最大的 `Component`

裡面包含了 `LoginScene`和`MainScene` 兩個`Component` (頁面)

透過 `this.state.auth.isLogin` 這個 `Boolean` 來作判斷要顯示哪一個 `Component`

所以如果在一開始進入這個網站的時候 預設 `this.state.auth.isLogin` 是 false

所以每次瀏覽第一頁一定是 `LoginScene`

而在送出登入之後會模擬發送 request

然後成功後 `this.state.auth.isLogin` 修改為 true

這時候會顯示 `MainScene`

利用這個機制來作頁面切換

### Boolean

為什麼要用`布林值`呢？

因為登入這個行為只有成功與失敗

特性和 Boolean 一樣所以才使用`布林值`

而在 `MainScene` 中也會依據  `this.state.route.currentPage` 的值透過 switch 顯示相對應的 `XXXScene`(頁面)

因為頁面部會只有兩頁

在多重選擇下以 switch 較為適合

`this.state.route.currentPage` 以字串來做各自頁面的判別

完成 頁面切換功能

## React Route

為什麼不直接使用 `React Route`?

或者應該說 什麼時候要使用 `React Route` 呢？

除非該頁面有需求直接透過 url 輸入來顯示該頁面

例如:  註冊, 忘記密碼....

才有使用 `React Route` 的意義

如果有使用 `redux` 之後每一個頁面都必須使用 `connect` 來做和 store 的綁定

其實是很麻煩並且消耗資源的一件事情