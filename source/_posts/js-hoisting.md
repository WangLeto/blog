---
title: JavaScript变量提升
date: 2018-12-28 16:59:04
tags: JavaScript
---

ES 5 以及之前的版本只有 `全局作用域` 和 `函数作用域` 两种作用域，在 ES 6 标准中引入了块级作用域这个概念，随之新增了 `let` 与 `const` 两种变量声明方式。

```javascript
x = "global"; // 挂载到全局作用域中

// 函数作用域
(function() {
    console.log(x); // 输出 undefined
    var x = 'local';
}());

// 块级作用域
{
    console.log(x); // 未声明的变量，运行出错
    let/const x = 'local';
}

// 闭包
(function() {
    console.log(x); // 输出 'global'
})
```

<!-- more -->

以上的函数作用域实际过程如下：

```javascript
(function() {
    var x; // 由变量提升而被声明
    console.log(x);
    x = 'local'; // 赋值
}())
```

块级作用域的过程：

````javascript
{
    // 由于变量提升，创建 x，但是 x 未被进行词法绑定，处于暂时死区(temporal dead zone)
    console.log(x);
    let x; // 此时 x 从暂时死区中离开，已经可以访问
    x = 'local'; // 赋值
}
````

巧妙玩弄(?) 暂时死区：

```javascript
let x = x;  // Uncaught ReferenceError: x is not defined

let x = 1;  // Uncaught SyntaxError: Identifier 'x' has already been declared

console.log(x); // Uncaught ReferenceError: x is not defined
```

x 被困在暂时死区中，此后该作用域中 x 无法使用。

## 参考

[我用了两个月的时间才理解 let](https://zhuanlan.zhihu.com/p/28140450)

[Are variables declared with let or const not hoisted in ES6?](https://stackoverflow.com/a/31222689/7526989)

[ES6 中 let 暂时性死区详解](https://segmentfault.com/a/1190000015603779)