---
title: 常规操作：安卓使用 xml 文件代替普通位图图标
date: 2018-05-18 14:41:08
tags: Android
---

Android 的 drawable 资源文件通常需要多个尺寸的位图，分别放到对应的文件夹下，以完成不同尺寸设备的适配工作。

那么有没有办法使用矢量文件来在自动完成适配呢？答案是可以的。虽然 Android 不支持直接解析 svg 矢量文件，但是别忘了 Android 开发的好基友——xml 同学。

<!--more-->

## 下载所需的矢量图

首推 [阿里巴巴矢量图标库](http://www.iconfont.cn/)，这里是国内少有的免费设计师图标分享网站，我在大二的时候做 PPT 就经常在这里找各种高逼格的漂亮小图标了~

使用你的 github 账号登录后，搜索你所需的图标。英文通常比中文的搜索效果要好一些。

注意右侧现在有了精选、多彩两个选项，实在非常方便。

![](http://qiniu1.letow.top/snipaste%202018.05.18-14.20.jpg)

选择所需的图标后，点击下载按钮，出现的对话框中，我们还可以对图标颜色进行进一步定制。方法是点选需要修改颜色的部分，然后选取或输入所需颜色。注意颜色修改不会自动取消选区，也就是说需要取消上一个修改颜色的部分，再选取下一个部分，不然会连续选取区域（多么反人类的 UX）。此外可以自定义图标的大小，主要是对 png 图片。

![](http://qiniu1.letow.top/snipaste%202018.05.18-14.21.jpg)

颜色选取完成，下载 svg 图片。



## ~~转为 xml 文件~~

~~因为 Android 并不能直接解析 svg 矢量图标，因此我们需要将其转为 xml 资源文件。~~

~~使用 [Android SVG to VectorDrawable](http://inloop.github.io/svg2android/) 这个网站（需要翻墙），或类似网站，即可方便地进行文件转换。~~



---

modify in 当晚

## 引入项目

原解决方案显得过于繁琐，这里给出更好的解决方案：使用 Android studio 自身强大的功能。直接引入就可以了！

![](http://qiniu1.letow.top/snipaste%202018.05.18-21.07.jpg)

![](http://qiniu1.letow.top/snipaste%202018.05.18-21.10.jpg)

相比第三方网站给出的转换，Android studio 不仅操作简便，甚至还完美支持了渐变色的属性。

参考：[Android使用矢量图（SVG, VectorDrawable）实践篇](https://www.jianshu.com/p/0555b8c1d26a)



## tips & bugs

1. 使用 textView 时用到了 `drawbleleft` 这个属性，发现此时 xml 文件大小不合适，又不会手动改怎么办？

   参考我的这篇文章：[解决 TextView 中 drawableleft 图片大小不可控问题](http://blog.letow.top/2018/04/25/drawable-size-set/)

2. Android studio 出现了奇怪的报错

   我忘了具体的报错内容是什么了，总之与使用的 xml 文件有关。仔细观察转换得到的 xml 文件发现其中的某些 `fillColor` 字段为空，此时根据你的矢量图颜色将其补全即可。（使用16进制的 RGB 色值，取色可以使用 PPT 或 Snipaste 等软件）

3. 使用矢量图的更多问题和文章在网上有很多，比如：[Android Vector曲折的兼容之路](https://www.jianshu.com/p/e3614e7abc03)
