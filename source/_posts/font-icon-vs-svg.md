---
title: 小图标的最优解： svg
date: 2019-01-20 10:22:08
tags: CSS
---

最近为了实现一个动画效果使用了 `::before` 伪元素，里面需要显示一个 icon。经过一番查找，我发现有这么几种方案：

- CSS sprite
- font-face
- svg 

## 预备知识

### 网络请求的速度

网站加载时会请求所需的静态资源文件，这其中包括图片文件。网站加载的一个重要影响因素不一定是所请求资源的大小，反而是资源文件的数量。每个请求从建立到完成不仅仅是下载文件下载用时，事实上，对于小文件来说，真正的下载时间仅占请求的一小部分。

![](http://qiniu1.letow.top/snipaste%202019.01.20-11.05.jpg)

<!--more-->

上图是一个七牛云外链图片的下载时间，可以看到，真正的下载时间只有 3.84ms，而整个请求的完成时间是 29.22ms。

此外，浏览器的并发请求数量是有限制的。也就是说，虽然浏览器通过对多个静态资源同时开始请求减少因为连接用时等带来的时间浪费，但也仅仅是部分提升。如果网页需要的静态文件过多，并发请求的优势也会被抹平。

### 位图与矢量图

关于位图和矢量图，程序员应该都明白其差别。简单点说就是画板和几何画板的区别。位图缩放会失真，变成像素图；矢量图拥有“无限细节”，可以随意放缩。显然，拥有简单轮廓的图标适合使用矢量图来表示。

## Png + CSS sprite

这是一种上古年间的实现方式，sprite 技术又被称为“精灵图”或“雪碧图”，是指将很多小图按照格子图一样的形式布局在一整张大图上，从而避免对大量小图片的请求，加快网页加载速度。在使用时，通过CSS进行图片定位，就可以取得所需的图标。

但是使用位图作为图标的时代已经过去了，高分辨率屏幕的普及使得位图日暮西山，精灵图再见！

## font-face 方式使用图标

`font-face` 因为 bootstrap 推荐的 font-awesome 而出名，是一种简单的使用位图作为图标的方案。而且通过将众多小图标整合到一套字体中去，font-face 也无需加载大量小文件，只需请求一次引用的字体文件即可。

简要介绍使用方法：

```css
@font-face {
  font-family: 'example';
  src: url(example.ttf);
  unicode-range: U+0025-00FF;
}
```

通过以上的 font-face 声明，定义了一种名为“example”的字体，任何 font-family 为 “example”、字符的 unicode 值在 `U+0025-00FF` 范围内的文字，都会使用在 `example.ttf` 这个字体文件中的样式。而字体文件对这些 unicode 值的样式，就是我们所期望的图标了。

font-face 的详细使用方法：[真正了解CSS3背景下的@font face规则](https://www.zhangxinxu.com/wordpress/2017/03/css3-font-face-src-local/)。如果想要自定义字体文件，[阿里巴巴矢量图标库](http://www.iconfont.cn)，[icomoon.io](icomoon.io) 都可以满足你的需要。

虽然广受欢迎，但是比起 svg，font-face 的解决方案还是显得不够优秀。两种方案的对比优缺点可以参考这里：[Inline SVG vs Icon Fonts [CAGEMATCH]](https://css-tricks.com/icon-fonts-vs-svg/)。可以看到，除了兼容性略逊一筹，svg 的效果全方面超过 font-face。

## svg

svg 的优点就不再赘述了。可能有人会有大量 svg 加载的疑虑，但是属于 svg 的精灵图技术也是早已存在的：[未来必热：SVG Sprite技术介绍](https://www.zhangxinxu.com/wordpress/2014/07/introduce-svg-sprite-technology/)。

其代码大致如下：

```xml
<svg>
  <defs>
    <g id="shape">
        <rect x="0" y="0" width="50" height="50" />
        <circle cx="0" cy="0" r="50" />
    </g>
  </defs>

  <use xlink:href="#shape" x="50" y="50" />
  <use xlink:href="#shape" x="200" y="50" />
</svg>
```

在同一个 svg 文件中定义多个 `g` ，并通过 `use` 标签的 `xlink` 索引其 id 后重命名导出。使用如下：

```xml
<li><svg><use xlink:href="target"/></svg>文字</li>
```

而且与位图 base64 的编码写入 css 相仿，svg 也可以直接内联到 `url()` 方法中，仅需仅需简单转换即可，相关文章：[学习了，CSS中内联SVG图片有比Base64更好的形式](https://www.zhangxinxu.com/wordpress/2018/08/css-svg-background-image-base64-encode/)。

以上链接中提到的相关工具网站：[SVG在线压缩合并工具](https://www.zhangxinxu.com/sp/svgo/)。



{% raw %}
<svg xmlns="http://www.w3.org/2000/svg" version="1.2" viewBox="0 0 600 600" width="50%" style="margin: 0 25%">
    <path fill="#EF914E" stroke="#000" stroke-width="3" d="M65 153l140-83C179 17 126-7 88 15s-49 85-23 138"/>
    <path fill="#F2F2D8" stroke="#000" stroke-width=".5" d="M110 110l52-31c-9-20-29-28-43-20-15 8-18 31-8 51"/>
    <path fill="#EF914E" stroke="#000" stroke-width="3" d="M554 194c36-47 39-108 5-139-32-29-89-17-124 29l118 110"/>
    <path fill="#F2F2D8" stroke="#000" stroke-width=".5" d="M522 140c13-17 14-41 2-53-12-11-34-6-48 10l45 42"/>
    <path fill="#ED924E" stroke="#000" stroke-width="3" d="M302 42c10 0 42 0 83 12 27 8 118 35 169 119 20 33 27 66 30 78 2 10 6 35 4 71-2 47-7 122-65 185-55 61-128 74-155 79-56 9-100 0-123-6-41-7-162-36-214-145-40-86-14-173-7-195 12-43 32-71 37-78 52-74 130-98 157-107 12-4 48-13 84-13z"/>
    <circle cx="195" cy="196" r="22" stroke="#000"/>
    <circle cx="421" cy="220" r="24" stroke="#000"/>
    <path d="M430 149c27-2 53 1 66 10-6-14-34-24-66-22-36 1-65 17-65 34v2c11-11 36-21 65-24zm-231-9c-31-2-59 2-73 12 7-15 38-27 73-25 41 1 73 17 73 37v2c-11-13-39-23-73-26z"/>
    <path fill="#994E27" d="M294 363c-16 25-52 44-94 44h-11c0 3 180 18 180 18s-46 0-75-62z"/>
    <path fill="#F2B8B0" stroke="#000" stroke-width="2" d="M187 408c12 13 48 45 100 50 53 5 84-33 84-33s-27-7-31-9c-15-4-16-6-29-10-12-3-54-5-67-2-25 2-45 3-57 4z"/>
    <ellipse cx="299" cy="329" fill="#ED924E" stroke="#000" stroke-width="2" rx="40" ry="38"/>
    <path fill="none" stroke="#000" stroke-miterlimit="10" stroke-width="2" d="M416 413c-8 5-23 11-41 11-32 0-63-23-79-57m-2 0c-16 27-106 68-166 14"/>
    <path fill="none" stroke="#000" stroke-width="2" d="M231 480c20 17 80 23 103 5"/>
    <path fill="#E8E8E8" stroke="#000" d="M492 306s-16 43-14 62c1 19 24 18 27 4 2-14-12-67-12-67"/>
</svg>

{% endraw %}