---
title: visual studio 快速切换项目
date: 2018-12-20 03:46:21
tags: [其他, visual studio]
---

![](http://qiniu1.letow.top/snipaste%202018.12.20-03.47.jpg)

> visual studio，你又出 bug 了！
>
> 卡住不能算 bug……卡住！…… IDE 的事情，能算 bug 么？

## 同一解决方案下创建多个项目

手上的程序都很小，还有些 demo，何必每个程序都开一个 visual studio 实例。果然，右键“解决方案”部分，可以新建/添加项目。

## 切换运行的项目

不怕麻烦的话，大可以每次都右键解决方案进行配置——谁会这么做呢？

于是大佬出现了：[Quick-switching startup projects in Visual Studio 2012](https://stackoverflow.com/a/16875065/7526989)，该答案提供了作者自己写的 visual studio 扩展 [SwitchStartupProject](https://marketplace.visualstudio.com/items?itemName=vs-publisher-141975.SwitchStartupProject)。效果如图所示。

当然，只是进行生成而不是运行的话，直接右键点击项目名称就可以了。