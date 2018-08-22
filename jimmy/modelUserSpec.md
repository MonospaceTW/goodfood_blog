## Model User Spec

這個 User Spec 要我們分別測試對 Database 中 User 的 Create、Update、Destroy，
我們使用 sequelize 這個基於 Promise 的 Node.js ORM 來操作我們的 Database，

這邊解釋一下我們會常看到的常數，
SPEC_MODEL_NAME 是我宣告的常數代表 User Model，

fakeData 是測試用的假資料具體可參考以下連結：

[User Spec](https://github.com/MonospaceTW/goodfood_bot/blob/develop/test/unit/models/user.spec.js)

---

每個 describe 就是我們要測的 unit( function ) ，
我們可以在 describe 或 it 後面加上 only ，這樣能夠聚焦在那個 unit 中，
當然 unit 裡還會有更小的 unit，就是每個 it，
或是在 describe 或 it 後面加上 skip 來跳過任何你想跳過的 unit 。

我們可以透過 sequelize 提供的 create 來新增 user 資料到 user Table 裡，
由於對資料庫的操作都是非同步的，大部分的情況等不到資料撈回來就會繼續往下執行，
這時資料可能讀寫不完全或還沒讀寫到，
不過我們可以使用 async await 來解決這個問題，

另外一提，使用 console.log 時建議使用以下方式來查詢會比較方便查找內容：
```javascript
console.log('target=>', target);
```

以下是一些範例：

### 新增 User 的方式：


```javascript
  describe('Create model data', () => {
    it('creates a data', async () => {
      // TODO:
      // 1. model.create(fakeData.create)...
      // 2. use data from `fakeData.create`
      const createdModelData = null;

      Object.keys(fakeData.create).forEach(e => {
        expect(createdModelData[e]).to.equal(fakeData.create[e]);
      });
    });
  });
```

目前我們的 createdModelData = null;

我們將 null 修改如下：
```javascript
const createdModelData = await models[SPEC_MODEL_NAME].create(fakeData.create);
```
我們將要新增的資料當作 create 的參數即可。

接著可以看到
```javascript
Object.keys(fakeData.create).forEach(e => {
  expect(createdModelData[e]).to.equal(fakeData.create[e]);
});
```

這邊使用 expect.js 來驗證新增到 Database 的資料是否符合預期，之後也會繼續使用。

---

### 接著來看更新 user 的方法：

```javascript
  describe('Update model data', () => {
    let modelDataForUpdate = null;
    before(async () => {
      // create a model data before run test
      modelDataForUpdate = await models[SPEC_MODEL_NAME].create(fakeData.update);
    });

    it('update a model with find and save', async () => {
      // TODO:
      // 1. use model.find(modelId === updatedModel.id) then model.save();
      // 2. use data from `fakeData.updateNewData`
      // 3. use `findAndUpdatedModel` as target, `modelDataForUpdate.id` as condition.
      let findAndUpdatedModelData = await models[SPEC_MODEL_NAME].findOne({
        where: {
          id: modelDataForUpdate.id
        }
      });
      findAndUpdatedModelData.nickName = fakeData.updateNewData.nickName;
      findAndUpdatedModelData.email = fakeData.updateNewData.email;
      await findAndUpdatedModelData.save();

      Object.keys(fakeData.updateNewData).forEach(e => {
        expect(findAndUpdatedModelData[e]).to.equal(fakeData.updateNewData[e]);
      });
    });

    it('update a model with update method', async () => {
      // TODO:
      // 1. use model.update()...
      // 2. use data from `fakeData.updateNewData2`
      // 3. use `updateModelData` as target, `modelDataForUpdate.id` as condition.
      const updateModelData = await modelDataForUpdate.update(fakeData.updateNewData2, {
        where: {
          id: modelDataForUpdate.id
        }
      });

      Object.keys(fakeData.updateNewData2).forEach(e => {
        expect(updateModelData[e]).to.equal(fakeData.updateNewData2[e]);
      });
    });
  });
```

每個 it 執行前，都會先執行 before 裡的程式碼，

因為要測試更新會員所以必須先新增 user 資料，

這邊我們先新增一筆 user 資料，如下：
```javascript
before(async () => {
  // create a model data before run test
  modelDataForUpdate = await models[SPEC_MODEL_NAME].create(fakeData.update);
});
```

這個 unit 是針對更新 user 來測試，分別有兩個子 unit ，我們依序來看，
第一個子 unit 要我們 update a model with find and save，

由於 sequelize 新增 user 時， 會產生對應的 id，
因此我們可以透過這個 id 找出新增的 user 並將其更新，
具體作法如下：

```javascript
let findAndUpdatedModelData = await models[SPEC_MODEL_NAME].findOne({
  where: {
    id: modelDataForUpdate.id
  }
});
findAndUpdatedModelData.nickName = fakeData.updateNewData.nickName;
findAndUpdatedModelData.email = fakeData.updateNewData.email;
await findAndUpdatedModelData.save();

```

可以看到我們先從 Database 的 User 中，
透過 sequelize 的 findOne 方法來查詢資料，
findOne 中接受一個參數用來找尋符合的資料，
這個參數是個物件，透過 where 來搜尋 User 中符合條件的資料，
where 的屬性值也是個物件，在物件裡透過 Key 與 Value 來查詢符合條件的資料，

因為這個範例接著要我們修改資料與 save ，所以就透過賦值的方式來直接修改，
修改完之後在進行 save 即可，

第二個子 unit 要我們 update a model with update method ，
這邊使用 sequelize 的 update 方法來更新 user 資料，

```javascript
const updateModelData = await modelDataForUpdate.update(fakeData.updateNewData2, {
  where: {
    id: modelDataForUpdate.id
  }
});
```
update 方法中目前有兩個參數，第一個參數是新的資料， 第二個參數是你要對哪些符合條件的資料更新，
這邊使用 id 來當條件，modelDataForUpdate.id 是新增 user 時所產生的，如果條件符合就會對符合條件的資料進行更新。

---

### 查詢單筆資料

在這個 unit 我們可以透過 sequelize 的 findOne 方法來查詢符合條件的資料，
程式碼如下：

```javascript
describe('Find one model data', () => {
  let modelDataForFind = null;
  before(async () => {
    // create a model before run test
    modelDataForFind = await models[SPEC_MODEL_NAME].create(fakeData.findOne);
  });

  it('find a model with findOne', async () => {
    // TODO:
    // 1. use model.findOne...
    // 2. use data from `fakeData.findOne`
    // 3. use `findModelData` as target, `modelDataForFind.id` as option.
    const findModelData = await models[SPEC_MODEL_NAME].findOne({
      where: {
        id: modelDataForFind.id
      }
    });

    Object.keys(fakeData.findOne).forEach(e => {
      expect(findModelData[e]).to.equal(fakeData.findOne[e]);
    });
  });
});
```

執行這個 unit 測試前先在 before 新增 user 資料，
```javascript
before(async () => {
  // create a model before run test
  modelDataForFind = await models[SPEC_MODEL_NAME].create(fakeData.findOne);
});
```

接著查詢與新增的 user 相同 id 的那筆資料，

```javascript
const findModelData = await models[SPEC_MODEL_NAME].findOne({
  where: {
    id: modelDataForFind.id
  }
});

```

### 查詢多筆資料

這個 unit 我們可以透過 sequelize 的 findAll 方法來查詢符合條件的資料，
程式碼如下：

```javascript
describe('Find all model data', () => {
  before(async () => {
    // create 3 models and push into models array.
    // await models[SPEC_MODEL_NAME].create(data1);
    await models[SPEC_MODEL_NAME].bulkCreate(fakeData.findAll);
  });

  it('find all model', async () => {
    // TODO:
    // 1. use model.findAll...
    // 2. use data from `fakeData.findAll`
    // 3. use `findAllModelData` as target.
    const findAllModelData = await models[SPEC_MODEL_NAME].findAll();

    expect(findAllModelData.length).to.greaterThan(fakeData.findAll.length - 1);
  });

  it('find all with where', async () => {
    // TODO:
    // 1. use model.findAll...
    // 2. use data from `fakeData.findAll`
    // 3. use `findAllModelData` as target, and use `fakeData.keyword` as option.
    const findAllModelData = await models[SPEC_MODEL_NAME].findAll({
      where: {
        nickName: fakeData.keyword.nickName
      }
    });

    expect(findAllModelData.length).to.greaterThan(fakeData.findAll.length - 1);
    fakeData.findAll.forEach(i => {
      Object.keys(i).forEach(e => {
        // console.log('fakeData.findAll[e]=>', fakeData.findAll[e]);
        expect(findAllModelData[e]).to.equal(fakeData.findAll[e]);
      });
    });
  });
});
```

這個 unit 有兩個子 unit ，
在 run 這兩個子 unit test 前會先執行 before 裡的程式碼來新增 user 資料，
```javascript
  before(async () => {
    // create 3 models and push into models array.
    // await models[SPEC_MODEL_NAME].create(data1);
    await models[SPEC_MODEL_NAME].bulkCreate(fakeData.findAll);
  });
```
這次透過 sequelize 的 bulkCreate 方法來增加多筆資料，

第一個子 unit 要我們 find all model，
這邊我們使用 findAll 找出多筆資料，如果沒有給參數會查詢出所有 user 資料，
程式碼如下：
```javascript
const findAllModelData = await models[SPEC_MODEL_NAME].findAll();
```

第二個子 unit 要我們 find all with where，
我們給 findAll 參數，來當作查詢的條件，
程式碼如下：
```javascript
const findAllModelData = await models[SPEC_MODEL_NAME].findAll({
  where: {
    nickName: fakeData.keyword.nickName
  }
});
```
這樣我們就會查詢出符合條件的 user

---

### 刪除單筆資料

這個 unit 我們可以透過 sequelize 的 destory 來刪除單筆資料，
程式碼如下：
```javascript
describe('Destroy model data', () => {
  let modelDataForDestroy = null;
  before(async () => {
    // create data before run test
    modelDataForDestroy = await models[SPEC_MODEL_NAME].create(fakeData.destroy);
  });

  it('delete a model data with where', async () => {
    // TODO:
    // 1. use model.destroy...
    // 2. use data from `fakeData.findAll`
    // 3. use `deleteModelData` as target, and use `modelDataForDestroy.id` as option.
    const deleteModelData = await models[SPEC_MODEL_NAME].destroy({
      where: {
        id: modelDataForDestroy.id
      }
    });

    const findDeletedModel = await models[SPEC_MODEL_NAME].findOne({
      where: {
        id: modelDataForDestroy.id
      }
    });

    expect(deleteModelData).to.equal(1);
    expect(findDeletedModel).to.equal(null);
  });
});
```

在 run 這個 unit test 前會先執行 before 裡的程式碼來新增一筆 user 資料，

```javascript
before(async () => {
  // create data before run test
  modelDataForDestroy = await models[SPEC_MODEL_NAME].create(fakeData.destroy);
});
```

現在 destory 方法中的參數就是用來當作刪除條件，將資該筆 user 資料刪除，
```javascript
const deleteModelData = await models[SPEC_MODEL_NAME].destroy({
  where: {
    id: modelDataForDestroy.id
  }
});
```

---

### 新增 User 並關聯 Passport

這個 unit test 程式碼如下：
```javascript
describe('Associated model data', () => {
    it('Create a model with associated data using include', async () => {
      // TODO:
      // 1. create a User with 3 passports.
      // 2. use model.create with include.
      // 3. use data as Passport data, use `fakeData.create2` as user data.
      const data = [{
        token: '1',
        workspaceName: 'ws1',
        passwordHash: 'ws1ws1ws1',
      }, {
        token: '2',
        workspaceName: 'ws2',
        passwordHash: 'ws2ws2ws2',
      }, {
        token: '3',
        workspaceName: 'ws3',
        passwordHash: 'ws3ws3ws3',
      }];

      const user = await models[SPEC_MODEL_NAME].create({
        ...fakeData.create2,
        Passports: data
      }, {
        include: [models.Passport]
      });

      expect(user.nickName).to.be.equal(fakeData.create2.nickName);
      expect(user.email).to.be.equal(fakeData.create2.email);
      expect(user.Passports.length).to.equal(data.length);
    });

    it('Create a model with associated data using set()', async () => {
      // TODO:
      // 1. create a User and use set() method to set 1 associated model data.
      // 2. use model.findOne() with include to get model data with associated data.
      // 3. use data as Passport data, use `fakeData.create3` as user data.
      const data = {
        token: '1',
        workspaceName: 'ws1',
        passwordHash: 'ws1ws1ws1',
      };

      let user = await models[SPEC_MODEL_NAME].create({
        ...fakeData.create3,
        Passports: data
      }, {
        include: [models.Passport]
      });

      const passport = await models.Passport.create(data);
      await user.setPassports(passport);

      const userWithPassport = await models[SPEC_MODEL_NAME].findOne(
        {
          where: {
            nickName: fakeData.create3.nickName
          },
          include: [models.Passport],

        }
      );

      expect(userWithPassport.nickName).to.be.equal(fakeData.create3.nickName);
      expect(userWithPassport.email).to.be.equal(fakeData.create3.email);
      expect(userWithPassport.Passports.length).to.equal(1);
      expect(userWithPassport.Passports[0].token).to.equal(data.token);
      expect(userWithPassport.Passports[0].workspaceName).to.equal(data.workspaceName);
      expect(userWithPassport.Passports[0].passwordHash).to.equal(data.passwordHash);
    });
  });
});
```

第一個子 unit 要我們 Create a model with associated data using include ，
我們一樣透過 create 方法來新增 user 資料但我們必須關聯 passport，

這邊 create 方法的第一個參數我們透過解構賦值的方式來加入 Passports 屬性與屬性值，
第二個參數是個物件可用來關聯其他 Model ，這邊新增的 user 資料要與 Passport Model 關聯，
我們透過 include 來關聯，
程式碼如下：

```javascript
let user = await models[SPEC_MODEL_NAME].create({
  ...fakeData.create3,
  Passports: data
}, {
  include: [models.Passport]
});
```

第二個子 unit 要我們 Create a model with associated data using set() ，
這邊 create 時與第一個子 unit 相同，
程式碼如下：
```javascript 
let user = await models[SPEC_MODEL_NAME].create({
  ...fakeData.create3,
  Passports: data
}, {
  include: [models.Passport]
});
```

但是我們必須使用 set 來將 passport 新增進 user 裡，
所以我們要先透過 create 方法來新增 passport 到 Passport Model 裡，

```javascript
const passport = await models.Passport.create(data);
```
在來就是處理 set 的部份，
我們有先在 Passport Model 中新增 passport 資料，
因此可以透過以下方式來將 passport 加進 user
```javascript
await user.setPassports(passport);

```

接著我們要用 findOne 方法查詢那筆有 passport 的 user 資料，

程式碼如下：
```javascript
const userWithPassport = await models[SPEC_MODEL_NAME].findOne(
  {
    where: {
      nickName: fakeData.create3.nickName
    },
    include: [models.Passport],
  }
);
```
在 findOne 方法中的這個參數不只要給查詢條件(透過 where)，
還要給關聯資料(透過 include 來關聯 Passport Model)，
這樣才能找到我們剛才透過 set 將 passport 加進 user 的這筆 user 資料。

---

### 參考連結：

[單元測試：Mocha、Chai 和 Sinon](https://cythilya.github.io/2017/09/17/unit-test-with-mocha-chai-and-sinon/)

[Node.js 入門之 Express.js 實戰-6.5使用資料庫-orm](https://github.com/iamcxa/node-tutorial/blob/master/node-tour/node-express/index.md#65-%E4%BD%BF%E7%94%A8%E8%B3%87%E6%96%99%E5%BA%AB-orm---sequelizejs)

[Sequelize Getting started](http://docs.sequelizejs.com/manual/installation/getting-started.html)

[Sequelize CRUD](https://ithelp.ithome.com.tw/articles/10187835)

[Sequelize-入门指南](https://my.oschina.net/zj0303/blog/305384)
































































