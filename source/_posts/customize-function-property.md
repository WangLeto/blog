---
title: 给函数添加自定义属性的八种实现
date: 2019-02-23 16:09:55
tags: JavaScript
---

本文同时发布于掘金的个人专栏：[[译] 为函数自定义属性的八种实现方法](https://juejin.im/post/5c70fd3f6fb9a04a06057dff)。

> 介绍：本文来自 Stack Overflow 上 [Adding custom properties to a function - John Slegers 的答案](https://stackoverflow.com/a/20734003/7526989)。“给函数自定义属性”并不是常规做法，但是答案中给出的思路涉及广泛，很值得学习。

首先，你要认识到标准的函数属性（ arguments，name，caller 和 length ）都不能被覆盖。所以，打消自定义的这些属性名的想法吧。

给函数添加自定义属性可以使用很多不同方法，这些都是跨浏览器兼容的。

![](http://qiniu1.letow.top/1_zWhOGf_PgX0nRTLZLmFpGg_%E7%9C%8B%E5%9B%BE%E7%8E%8B%281%29.jpg)

<!--more-->

## 方法一：执行函数时添加属性

```javascript
var doSomething = function() {
    doSomething.name = 'Tom';
    doSomething.name2 = 'John';
    return 'Beep';
};

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : 
doSomething.name2 : undefined
doSomething() : Beep
doSomething.name : 
doSomething.name2 : John 
```

### 方法一（替代语法）

```javascript
function doSomething() {
    doSomething.name = 'Tom';
    doSomething.name2 = 'John';
    return 'Beep';
};

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : doSomething
doSomething.name2 : undefined
doSomething() : Beep
doSomething.name : doSomething
doSomething.name2 : John 
```

### 方法一（第二种替代语法）

```javascript
var doSomething = function f() {
    f.name = 'Tom';
    f.name2 = 'John';
    return 'Beep';
};

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : f
doSomething.name2 : undefined
doSomething() : Beep
doSomething.name : f
doSomething.name2 : John 
```

这种方法的问题在于，你需要至少先执行一次函数，才能完成属性赋值。对于多数函数来说，很显然我们不想这么做。所以考虑其他选择。

## 方法二：定义函数之后添加属性

```javascript
function doSomething() {
    return 'Beep';
};

doSomething.name = 'Tom';
doSomething.name2 = 'John';

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : doSomething
doSomething.name2 : John
doSomething() : Beep
doSomething.name : doSomething
doSomething.name2 : John 
```

现在，你不需要在取得属性值之前运行一次函数了。然而，不足之处是感觉这些属性脱离了函数。

## 方法三：使用匿名函数包装函数体（并立即执行）

```javascript
var doSomething = (function(args) {
    var f = function() {
        return 'Beep';
    };
    for (i in args) {
        f[i] = args[i];
    }
    return f;
}({
    'name': 'Tom',
    'name2': 'John'
}));

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : 
doSomething.name2 : John
doSomething() : Beep
doSomething.name : 
doSomething.name2 : John 
```

使用匿名函数包装你的函数，就可以将属性放入对象中，然后在匿名函数内遍历这些属性加以添加。用这种方式，属性与函数联系密切了。当你想要从已存在的对象中拷贝属性到函数中时，这种技术很有效。

然而缺点是，你只能在定义函数时一次性添加这些属性。而且，这也违背了软件工程的 DRY 原则（[代码的抽象三原则](http://www.ruanyifeng.com/blog/2013/01/abstraction_principles.html)），尤其是你需要经常给各种函数添加属性的话。

## 方法四：为函数添加一个“extend”方法，用于从对象中逐个添加属性

```javascript
var doSomething = function() {
    return 'Beep';
};

doSomething.extend = function(args) {
    for (i in args) {
        this[i] = args[i];
    }
    return this;
}

doSomething.extend({
    'name': 'Tom',
    'name2': 'John'
});

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : 
doSomething.name2 : John
doSomething() : Beep
doSomething.name : 
doSomething.name2 : John 
```

可以一次性处理多个属性，也可以随时从别处添加了。

然而，代码同样违反了 DRY 原则，如果你经常这么做的话。

## 方法五：创建一个通用的“extend”函数

```javascript
// 注：原文有错误，已改正
var extend = function(obj, args) {
    if (Array.isArray(args) || (args !== null && typeof args === 'object')) {
        for (i in args) {
            obj[i] = args[i];
        }
    }
    return obj;
}

var doSomething = extend(
    function() {
        return 'Beep';
    }, {
        'name': 'Tom',
        'name2': 'John'
    }
);

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : 
doSomething.name2 : John
doSomething() : Beep
doSomething.name : 
doSomething.name2 : John 
```

这是一个通用的继承方法，更符合 DRY 原则，允许你任性添加属性。

## 方法六：创建一个 extendableFunction 对象，用它为函数添加扩展函数

```javascript
var extendableFunction = (function() {
    var extend = function(args) {
        if (Array.isArray(args) || (args !== null && typeof args === 'object')) {
            for (i in args) {
                this[i] = args[i];
            }
        }
        return this;
    };
    var ef = function(v, obj) {
        v.extend = extend;
        return v.extend(obj);
    };

    ef.create = function(v, args) {
        return new this(v, args);
    };
    return ef;
})();

var doSomething = extendableFunction.create(
    function() {
        return 'Beep';
    }, {
        'name': 'Tom',
        'name2': 'John'
    }
);

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : 
doSomething.name2 : John
doSomething() : Beep
doSomething.name : 
doSomething.name2 : John 
```

不再需要一个通用的“extend”函数了，这种方法生产出来的函数自带一个“extend”函数方法。

> 注：extendableFunction 和上面的 extend 都是高阶函数（high order function），但又都不是纯函数（pure function）。相关知识可以了解一下[函数式编程](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/)，有能力的话最好阅读英文原版 [mostly-adequate-guide](https://mostly-adequate.gitbooks.io/mostly-adequate-guide/content/)。

## 方法七：向 Function 原形挂载“extend”函数

```javascript
Function.prototype.extend = function(args) {
    if (Array.isArray(args) || (args !== null && typeof args === 'object')) {
        for (i in args) {
            this[i] = args[i];
        }
    }
    return this;
};

var doSomething = function() {
    return 'Beep';
}.extend({
    name : 'Tom',
    name2 : 'John'
});

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : 
doSomething.name2 : John
doSomething() : Beep
doSomething.name : 
doSomething.name2 : John 
```

这种做法的一个巨大优势是，向函数添加新的属性变得非常简单，这种做法也符合 DRY 原则，并且很面向对象。另外，向 Function 原形链挂载方法也节省内存。

然而，这种做法的缺点是并不具有充分的前瞻性。一旦将来浏览器给 Funcion 的原形上挂载了原生的“extend”方法，你的代码就会出问题。

## 方法八：函数递归一次然后返回自身

```javascript
var doSomething = (function f(arg1) {
    if(f.name2 === undefined) {
        f.name = 'Tom';
        f.name2 = 'John';
        // 注：此处原答案疑似错误，已改正
        f.extend = function(args) {
            if (Array.isArray(args) || (args !== null && typeof args === 'object')) {
                for (i in args) {
                    this[i] = args[i];
                }
            }
            return this;
        };
        return f;
    } else {
        return 'Beep';
    }
})();

console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
console.log('doSomething() : ' + doSomething());
console.log('doSomething.name : ' + doSomething.name);
console.log('doSomething.name2 : ' + doSomething.name2);
```

输出：

```
doSomething.name : f
doSomething.name2 : John
doSomething() : Beep
doSomething.name : f
doSomething.name2 : John 
```

运行该函数一次，检查它所需要的属性。如果没有，设置该属性并返回函数本体；如果设置过了，直接执行该函数。

如果你将“extend”方法设置为属性之一，之后还可以为该函数新增别的属性。

---

## 向对象中添加自定义属性

我虽然给出了这么多方法，还是建议你不要给函数设置属性；设置对象的属性好多了！

我个人喜欢使用下面这种语法来实现单例类：

```javascript
var keyValueStore = (function() {
    return {
        'data' : {},
        'get' : function(key) { return keyValueStore.data[key]; },
        'set' : function(key, value) { keyValueStore.data[key] = value; },
        'delete' : function(key) { delete keyValueStore.data[key]; },
        'getLength' : function() {
            var l = 0;
            for (p in keyValueStore.data) l++;
            return l;
        }
    }
})();
```

这种写法的好处是兼容公共和私有变量。例如，这样你就可以让“data”变为 private 的：

```javascript
var keyValueStore = (function() {
    var data = {};

    return {
        'get' : function(key) { return data[key]; },
        'set' : function(key, value) { data[key] = value; },
        'delete' : function(key) { delete data[key]; },
        'getLength' : function() {
            var l = 0;
            for (p in data) l++;
            return l;
        }
    }
})();
```

可是你想要多个 datastore 实例？没问题！

```javascript
var keyValueStore = (function() {
    var count = -1;
    
    return (function kvs() {
        count++;
        return {
            'data': {},
            'create': function() { return new kvs(); },
            'get' : function(key) { return this.data[key]; },
            'set' : function(key, value) { this.data[key] = value; },
            'delete' : function(key) { delete this.data[key]; },
            'getLength' : function() {
                var l = 0;
                for (p in this.data) l++;
                return l;
            }
        }
    })();
})();
```

>注：使用时可以按照以下语法
>
>```javascript
>let s1 = keyValueStore;
>let s2 = s1.create();
>s1.set('a', 1);
>s1.set('b', 2);
>s2.set('c', 3);
>s1.count() === s2.count(); // 输出 true；count() 返回 kvs 的个数
>s1.getLength(); // 输出 2；getLength() 返回每个 kvs 自身的数据条数
>s2.getLength(); // 输出 1
>s1.get('c'); // 输出 undefined；因为“c”是 s2 所包含的
>```

最后，你可以隔离实例和单例类的属性，并在原形上给实例定义 public 方法。

```javascript
var keyValueStore = (function() {
    var count = 0; // Singleton private properties

    var kvs = function() {
        count++; // Instance private properties
        this.data = {};  // Instance public properties
    };

    kvs.prototype = { // Instance public properties
        'get' : function(key) { return this.data[key]; },
        'set' : function(key, value) { this.data[key] = value; },
        'delete' : function(key) { delete this.data[key]; },
        'getLength' : function() {
            var l = 0;
            for (p in this.data) l++;
            return l;
        }
    };

    return  { // Singleton public properties
        'create' : function() { return new kvs(); },
        'count' : function() { return count; }
    };
})();
```

使用上面的方法，好处有：

- 为对象声明多个实例
- 保有私有变量
- 定义类的变量

使用方法如下：

```javascript
kvs = keyValueStore.create();
kvs.set('Tom', "Baker");
kvs.set('Daisy', "Hostess");
var profession_of_daisy = kvs.get('Daisy');  // 输出 Hostess
kvs.delete('Daisy');
console.log(keyValueStore.count());  // 输出 1
```

>注：keyValueStore 是单例，create 与 count 是其自身方法，create 用于新建数据仓库 kvs，count 为生产的 kvs 计数；kvs 仅有 get、set、delete、getLength 方法，无法像前一种实现一样直接调用 create 和 count 方法了。