---
title: JavaScript 包装对象
date: 2019-02-13 09:19:39
tags: JavaScript
---

参考：[包装对象](https://wangdoc.com/javascript/stdlib/wrapper.html)，[Why does instanceof return false for some literals?](https://stackoverflow.com/questions/203739/why-does-instanceof-return-false-for-some-literals)，[数据类型的转换](https://wangdoc.com/javascript/features/conversion.html)。

## 常规使用

强制转换主要指使用`Number()`、`String()`和`Boolean()`三个函数，手动将各种类型的值，分别转换成数字、字符串或者布尔值。

规则非常烦人，直接看链接：[Number()](https://wangdoc.com/javascript/features/conversion.html#number)。

另外 MDN 并不推荐直接声明包装对象，MDN：

> 不要用创建 `Boolean` 对象的方式将一个非布尔值转化成布尔值，直接将 `Boolean` 当做转换函数来使用即可

```javascript
var x = Boolean(expression);     // 推荐
var x = new Boolean(expression); // 不太好
```



Stack Overflow 上的问题：

```javascript
var color1 = new String("green");
color1 instanceof String; // returns true
var color2 = "coral";
color2 instanceof String; // returns false (color2 is not a String object)
```

所以不要滥用包装对象。

<!--more-->

三种包装对象各自提供了许多实例方法。有两种它们共同具有、从`Object`对象继承的方法：`valueOf`和`toString`。

```javascript
new Number(123).valueOf()  // 123
new String('abc').valueOf() // "abc"
new Boolean(true).valueOf() // true

new Number(123).toString() // "123"
new String('abc').toString() // "abc"
new Boolean(true).toString() // "true"
```

## 自动转换

原始类型的值，可以自动当作包装对象调用，即调用包装对象的属性和方法。这时，JavaScript 引擎会自动将原始类型的值转为包装对象实例，在**使用后立刻销毁实例**。

一个有意思的表现：

```javascript
let a = 'asd';
a.x = 2;                 // 2
console.log(a.x);        // undefined
```

因为添加的 `x` 属性实际上被挂载到了自动转换时产生的临时的 String 对象上了。

包括使用 `length` 属性获取字符串的长度，也是如此。



JavaScript 遇到预期为数值的地方，就会将参数值自动转换为数值。

```javascript
'5' - '2' // 3
'5' * '2' // 10
true - 1  // 0
false - 1 // -1
```

除了加法运算符（`+`）**有可能**把运算子转为字符串，其他运算符都会把运算子自动转成数值。

**关于自动转换为字符串**

JavaScript 遇到预期为字符串的地方，就会将非字符串的值自动转为字符串。具体规则是，先将复合类型的值转为原始类型的值，再将原始类型的值转为字符串。

字符串的自动转换，主要发生在字符串的加法运算时。当一个值为字符串，另一个值为非字符串，则后者转为字符串。

这也就是的“加法运算符可能把运算子转为字符串”的原因。



另外 `Boolean` 的转换规则很迷：

```javascript
// 这两个尤其反直觉
Boolean('false') // true
Boolean(new Boolean(false)) //true
```

使用**双重否运算符**（`!`）也可以将任意值转为对应的布尔值，效果与直接包装一样。（最反人类应该就是 `new Boolean` 这种用法）

## 有意思的地方：自定义方法

对于包装对象，还是有可取之处的。比如在包装对象的原形上定义一些有用的方法。

```javascript
String.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

'abc'.double()
// abcabc

Number.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

(123).double()
// 246
```

上面代码在`123`外面必须要加上圆括号，否则后面的点运算符（`.`）会被解释成小数点。