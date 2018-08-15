# Good Food API Document

## 登入

route: `/api/admin/login`
method: `POST`

header: 

request:

```json=
    {
      "email": "a@b.c", // email
      "password": "12345678" //密碼
    }
```

response:
    
```json=
    {
        "token": "user_token",
        "nickName": "tomas",
        "Authorization": "eyJhbGciOiJIUzI1NiJ9.eyJpZCI6MTA4LCJkZXZpY2VUb" // JWT Token
    }
```

## 目前所有的 使用者列表
route: `/api/admin/users?curPage=1&perPage=10`
method: `GET`

header:
    api-token ==> 帶登入回傳的 Authorization

response:
```json=
{
    "success": true,
    "status": 200,
    "message": "successed",
    "data": {
        "paging": {
            "curPage": 1,   // 當前頁碼
            "perPage": 10,  // 每頁幾筆資料
            "lastPage": 5,  // 最後一頁頁碼
            "sort": "DESC", // 目前排序規則
            "total": 50,    // 資料庫總共有幾筆資料
        },
        "items": [{
            "id": 1,
            "nickName": "q12312",
            "email": "a@b.c",
            "firebaseId": "aaaa",
            "createdAt": "2018-08-31 00:00"
        }],
    },
}
```

## 建立一筆訂單(揪團)

route: `/api/admin/groups`
method: `POST`

header:
  api-token

request:
```json=
    {
        "title": 開團名稱
        "startedAt" : 活動開始時間
        "finishedAt" :  結束時間
        "restaurantId": 餐廳ID
        "remake": 備註
    }
```

response:
```json=
   {
    "success": true,
    "status": 200,
    "message": "successed",
    "data": {
        "id": "新建 GroupOrder 的 ID"
    },
}
```
## 揪團列表

route: `/api/admin/groups?curPage=1&perPage=10`
method: `GET`

header:
    api-token ==> 帶登入回傳的 Authorization


response:
```json=
{
    "success": true,
    "status": 200,
    "message": "successed",
    "data": {
        "paging": {
            "curPage": 1,   // 當前頁碼
            "perPage": 10,  // 每頁幾筆資料
            "lastPage": 5,  // 最後一頁頁碼
            "sort": "DESC", // 目前排序規則
            "total": 50,    // 資料庫總共有幾筆資料
        },
        "items": [
            {
                "id": 揪團 ID
                "startedAt": 開始時間
                "finishedAt": 結束時間
                "remark": 備註
                "restaurantId": 餐廳ID
                "restaurantName": 餐廳名稱,
                "restaurantImageUrl": 餐廳 logo
                "restaurantAddress": 地址
                "restaurantPhone": 電話
            },
            ...
        ]
    }
}

   
```

## 揪團詳情
route: `/api/admin/groups/{group_id}`
method: `GET`

header:
    api-token ==> 帶登入回傳的 Authorization
    
reqeust:
```json=
{}
```

response:
```json=
{
    "success": true,
    "status": 200,
    "message": "successed",
    "data": {
        "id": 揪團ID
        "startedAt": 揪團開始時間
        "finishedAt": 揪團結束時間
        "remark": 揪團備註
        "restarant": {
            "id" 餐廳 ID
            "name": 餐廳名稱,
            "imageUrl": 餐廳 logo
            "address": 地址
            "phone": 電話
            "startAt": 開始營業時間
            "finishAt": 結束營業時間
            "remark": 餐廳備註   
            "foods": [ // 餐廳菜單 --- 有需要在這裡給資料嗎？
                 { "name": "漢堡1", "price": 100, "remark": "" },
                 { "name": "漢堡2", "price": 150, "remark": "" },
            ],
            "createdAt": "2018-08-31 00:00",
        },
        "orders": [{
            "id": 訂單 ID
            "price": 料理價格
            "count": 幾份
            "subTotal": 小計
            "remark": 訂單備註
            "foodId": 料理 ID
            "foodName": 料理名稱
            "userId": 1, 
            "userNickName": "Kent",
            "createdAt": "2018-08-31 00:00",
            },
        ]
    }
}
```

## 增加訂單
route: `/api/admin/groups/{group_id}/order`
method: `POST`

headers:
    api-token

request:
```json=
{
    "groupId": 揪團 ID
    "foodId": 料理 ID
    "count": 幾份
    "remark": "加醬油，不要蔥"
}
```

response:
```json=
{
    "success": true,
    "status": 200,
    "message": "successed",
}
```
