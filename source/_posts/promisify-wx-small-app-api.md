---
title: 微信小程序 API Promise 化
date: 2018-03-24 23:40:59
tags: [JavaScript, 小程序]
---

写法：

```js
let result = await (new Promise(function(resolve, reject) {
  wx.scanCode({
    onlyFromCamere: true,
    success: function(res) {
      resolve(res.result)
    },
    fail: function(res) {
      resolve(null)
    }
  })
}))
```

别忘了在包含此 promise 的函数前使用 async，实际上这个函数就被包装成了一个大的 promise。

---

## 更新

```js
const showModalPromised = function(obj) {
  return new Promise(function(resolve) {
    obj = Object.assign(obj, {
      success: function(res) {
        resolve(res);
      }
    });
    wx.showModal(obj);
  });
}
```

> 古人说得好：你可以菜，但最好别一直菜。

