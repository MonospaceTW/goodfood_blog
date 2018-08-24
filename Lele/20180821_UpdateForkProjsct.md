---
title: 更新fork的專案
date: 2018-08-21
---
#### 問題：在發PR的時候遇到衝突
---
#### 解決方法 ：
- 同步fork的專案
- 解決衝突

---

#### 同步fork的專案步驟

1.查看目前的remote
```
$ git remote -v
```

2.添加remote，使用的位置則是我們fork的專案位置
````
git remote add upstream https://github.com/otheruser/repo.git
```
新增完以後在利用 `remote -v` 檢查是否有添加成功

3.更新
```
git checkout master
```

4.接著把 upstream 的 master 更新給拉進來
```
git pull upstream master
```

5.如果你的 master branch 有自己的 commit, 也可以用 rebase 來避免不必要的 merge 操作:
```
git pull --rebase upstream master
```
by 樂樂