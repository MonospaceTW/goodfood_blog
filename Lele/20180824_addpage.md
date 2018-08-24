---
title: 新增頁面
date: 2018-08-24
---

在頁面新增上看了其他頁面的新增方法

- 在 components 裡新增資料夾以及在該資料夾新增index.js
  由於我負責處理的issus是使用者列表的部分，所以我就新增了一個 `UserListScene`
- 開啟 MainScene.js 
  - import 剛剛在 components 新增的資料夾
    `import UserListScene from "./components/UserListScene";`
  - 新增 'case'
    ``` 
    case 'userList':
        return <UserListScene {...props} />;
    ```
    主要用於轉跳頁面(其他詳細原因還沒有釐清)  
  - 之後為了可以看到自己的頁面，所以現在 `HomeScene` 新增了一個按鈕可以轉到我開的頁面