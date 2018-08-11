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
        "nickName": "tomas"
    }
```

## 目前所有的 使用者列表
route: `/api/admin/users?curPage=1&limit=10`
method: `GET`

header:
    api-token

response:
```json=
{
    "success": true,
    "status": 200,
    "message": "successed",
    "data": {
        "items": [{
            "id": 1,
            "nickName": "q12312",
            "firebaseId": "aaaa",
            "createAt": "2018-08-31 00:00:00",
            "updateAt": "2018-08-31 00:00:00"
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
        "startAt" : 活動開始時間
        "finishAt" :  結束時間
        "restaurantId": 餐廳ID
    }
```

response:
```json=
   {
    "success": true,
    "status": 200,
    "message": "successed",
    "data": {
        "id": "新建 order 的 ID"
    },
}
```
## 揪團列表

route: `/api/admin/groups?curPage=1&limit=10`
method: `GET`

header:
    api-token


response:
```json=
{
    "success": true,
    "status": 200,
    "message": "successed",
    "data": {
        "items": [
            {
                "id": 訂單 ID
                "restaurantId": 餐廳ID
                "name": 餐廳名稱,
                "imageUrl": 餐廳 logo
                "address": 地址
                "phone": 電話
                "startAt": 開始時間
                "finishAt": 結束時間
                "remark": 備註
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
    api-token
    
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
        "items": [
            {
                "id": 餐廳ID
                "name": 餐廳名稱,
                "imageUrl": 餐廳 logo
                "address": 地址
                "phone": 電話
                "startAt": 開始時間
                "finishAt": 結束時間
                "remark": 備註
                "menu": 餐廳菜單 
                "orders": [
                    {
                        "orderId": 訂單 ID
                        "foodName": 料理名稱
                        "foodID": 料理 ID
                        "foodPrice": 料理價格
                        "count": 幾份
                        "options": []
                    },
                    ...
                ]
            }
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
    "orders": [
        {
            "id": 料理 ID
            "count": 幾份
            "options": []
        },
        ...
    ]
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
