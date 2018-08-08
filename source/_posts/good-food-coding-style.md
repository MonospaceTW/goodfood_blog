---
title: Guidlines and Best Practices
tags: documents
---

# goodfood Slack app Guidlines and Best Practices

開發上每位開發者都會去互相維護修改其他開發者的程式碼, 因此有良好的寫作風格與共同的習慣可以幫忙更方便快速的code review以及除錯。

這裡基本撰寫原則, 以及如何撰寫來避免一些常見的開發錯誤。 歡迎補充與指正。

> “So if you want to go fast, if you want to get done quickly, if you want your code to be easy to write, make it easy to read.”  
― [Robert C. Martin](https://www.goodreads.com/author/show/45372.Robert_C_Martin), [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.goodreads.com/work/quotes/3779106)


## Code Guildlines

主要依據 [Google JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html)

加上一些風格如下:

- 縮排為2格空白

- 變數命名採用駝峰式命名(lower camel case), **當為了強調是由資料庫欄位名字過來, 命名會使用連字號方式, 像是 member_id** (全小寫, 底線連字號)

-  常數或是共用變數, 採用 全大寫+連字號, 例如:  `AAA_BBB_CCC`

- 字串**一律使用單引號**

- 每個述句(statement)後面都需要結尾分號

- 相關聯的程式碼述句(statements)請放在一起, 不同性質的程式碼述句建議使用空白行隔開,  不一定要在第一次開發功能的時候拆分或是重構程式碼, 但是將現有相關述句用分行隔開有利於接手的開法者閱讀

- **注意:** VsCode需要安裝 [ESLint plugin](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 才會正常顯示ESLint所標示的錯誤

## 提交程式碼前

- 利用IDE檢查有沒有哪些被ESLint標出紅字, **檢查拼字有無錯誤(變數, 字串)** 有沒有少寫個n, **哪些module沒有import到**, 如果有寫測試, 要重新跑一次測試確認是否都正常

-  **檢查排版**, 該有的空格, 縮排有沒有正確,  **過長, 或過度複雜的運算式應該分行**(例如 FP風格的mapping,filter, reduce) , 更新的排版commit一次

-  移除沒有用到的除錯用的 `console.log` 以及不必要的註解,  commit一次

- 如果你寫的是前端, 那請打開dev tool 看console有沒有警告 (例如 array components 少加了key參數)

- **變數命名必須要能表示:  做什麼用, 為什麼會出現在這裡,  可以如何使用** (如果還需要註解, 那這個名字可以沒有辦法展現該變數的意圖), 如果在同個modules裡面一個叫做 `productInfo`, 一個叫做 `productData`,  一個叫做`product` 那 review 會很頭痛

- 變數名字要可以發音念得出來

- 變數命名方式, 因為JS沒有type check, 所以有些可以明白的寫出讓人理解回傳值是什麼 , 例如 : `hasMember`, `isEmpty`

- 變數命名方式可以省略贅字 (舉例),  單一個匿名函數的變數在不影響閱讀下, 可以用短變數名字(舉例)  **變數命名的長度應該跟其scope大小相呼應**

- 假設在同一個module裡,   `A` method 使用到 `B` method,  那 `B` method 應該會排在 `A` method上方: 

``` js
const B = () => {};
const A = () => {};
```

- 幾乎, 不應該會出現 `var`

- 每一段程式碼都是一段思緒, 不用急著拆分method, 請**利用空白行來分隔這些思緒**。

- 變數的宣告應該盡量靠近變數被使用到的地方。 以後如果這組程式碼片段要拆分, 也比較方便。

- Method請維持單一職責原則(Single Responsibility Principle), 一個method只處理一件事情, 這樣也方便撰寫測試與除錯, **查看你method有沒有違反這個原則, 只要你能從這個method抽出另外一個method就是了**

例如若有一個method: `loginUserAndGetGroups()` 就代表你可能需要拆成兩個methods: `loginUser()` 和 `getGroups()`

再來就需要觀察這拆分的methods有沒有成群出現的跡象, 如果有, 就可以考慮寫一個wrapper method: 

``` js
getShows();
getMovies();
getMusic();

getUserMedia(){
  getShows();
  getMovies();
  getMusic();
}
```

- Method名字可以說明method所做的事情, **如果你的method內容改了, 請務必檢查method名字有沒有辦法說明你改動的內容** ,  變數命名應該描述可能的副作用(side effects) --> **函式名稱要說到做到**

- 盡量遵守immutable方式, method傳入的參數, 禁止直接使用該參數來做修改, 這樣會不知道跟原來的差別是什麼（可以透過 ESLint [no-param-reassign](https://eslint.org/docs/rules/no-param-reassign#disallow-reassignment-of-function-parameters-no-param-reassign) 檢查） 

**bad**
```
const arrayPush = (arr) => {
  arr.push('oh!')
  return arr;
}

const newArr = arrayPush([]);
```
**good**
```
const arrayPush = (arr) => {
  return [...arr, 'ya!'];
}

const newArr = arrayPush([]);
```
- Return Early pattern : 降低程式碼數量,  降低邏輯複雜度,  提升可讀性, 避免不必要的運算

例如 ([範例來源](https://forum.freecodecamp.org/t/the-return-early-pattern/19364)): 

``` js
const willItBlend = obj => { 
	let itWillBlend = false; 

	if (typeof someObject === 'object') { 
		if (someObject.blendable ==== 'It will blend') { 
			itWillBlend = true; 
		} else { 
			itWillBlend = false; 
		} 
	} else { 
		itWillBlend = false;
	}

  return itWillBlend; 
};
```

使用 early return pattern: 

``` js
const willItBlend = obj => { 
	if (typeof someObject !== 'object') return false;
	if (someObject.blendable !== 'It will blend') return false;
	return  true; 
};
```
- 盡量使用 `? ... : ...` 縮寫降低 `if ... else ..` 程式碼數量。

```
    isEmpty
      ? doSomethingWhenEmpty
      : doSomethingWhenNotEmpty
```

- `? ... : ...` 的內容應該簡短容易理解為主,  如果述句過長應該分行, 如果`?`或`:`後面述句有多行, 應該將多行的述句拆出獨立成一個函式
    - **建議最多一層**   

- async/await 前先想想, 是不是每個步驟都需要依序執行, 有些東西是可以並行處理的
    - `但是`，只要有需要做非同步操作，一定要用 async/await。


- 撰寫 MySQL SQL template string的時候, **[Keyword以及保留字](https://dev.mysql.com/doc/refman/8.0/en/keywords.html) 一律大寫**,   `AS` , `OR` 都是 keyword要大寫

- SQL template string 格式應該要縮排, 縮排型式如下: 

```
UPDATE 
  helloWorld
SET 
  hello=true,
  world='world',
  token_to_product_id=${token_to_product_id}
WHERE hello=true
  AND world='world'
```

## Vscode 推薦套件
* eslint
* Import cost
* Path Intellisense
* Bracket Pair Colorizer
* embrace
* Git History
* GitLens
* markdownlint
* Nord
* npm intellisense
* Partial Diff
* Path Intellisense
* Prettier
* intell
* Rest Client: 搭配 product-service 專案下的 ./test/test.xxx.http 可以取代 postman 直接測試端點
* vscode-icons
* Auto Import
* .ejs
* Code Spell Checker
* TODO Hightlight
* markdown all in one
* [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker)
