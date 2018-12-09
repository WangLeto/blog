---
title: 关于正则
date: 2018-11-03 15:25:19
tags: 其他
---

好久没写博客了，今天用到了一点正则，上网搜索发现了一点实用的技巧，可以不编写程序就能实现一些复杂功能。软件：sublime text 3。

## 非贪婪模式

https://stackoverflow.com/questions/18574069/sublime-text-regular-expression-for-search-and-replace

```
abcdacd
```

使用 `a[a-z]+d` 匹配的话，将匹配整段文字，也就是默认的贪婪模式。实际上我想要的是以 a 开头，以 d 结尾的两段。此时使用非贪婪的 `a[a-z]+?d` 模式匹配即可。

## 任意字符串

https://blog.csdn.net/u011734144/article/details/52118700

如果我需要匹配含换行符在内的任意字符串，使用 `[\s\S]+` 匹配即可。`\s` 为空白符，`\S` 为非空白符。

## 使用匹配对象替换

https://stackoverflow.com/questions/11491954/is-it-possible-to-use-regexp-as-variables-to-do-find-and-replace-in-sublime-text

如果使用编程方法进行替换的话，python 还是有点麻烦的（其实是我不熟悉啦）。无所谓，我们可以用匹配对象分组的形式实现。

```
第三册 二十二 双眸粲粲如星
```

如果我想将第二个数字前后加上第和回两个字，就可以使用这个方法。

匹配 `(第[一二三]册\s)([一二三四五六七八九十]+)` 模式，替换为 `$1第$2回`，最终结果就是`第三册 第二十二回 双眸粲粲如星`，很简单吧。


