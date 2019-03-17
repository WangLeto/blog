---
title: 记一次移动端使用 rem 的兼容性问题
date: 2019-03-17 13:17:23
tags: CSS
---

在移动端使用 rem 进行切图适配应该是比较常见的一种手法，近日在工作中遇到了一次有趣（才怪）的兼容性问题。特此记之，同时梳理使用 rem 适配的思路，以及一个使用 sass 函数的小技巧 ( ✪ω✪ )。

本文同时发布在我的 [掘金专栏](https://juejin.im/post/5c8dd70fe51d45111f1d2089)。

## rem 与 em 的对比

rem 和 em 都是 CSS 中基于字体大小的相对单位，二者的区别在于：em 使用当前元素的字体大小确定实际尺寸，而使用 rem 依据的则是 html 根节点的字体大小。

使用 em 其实很恼人，例如分别为父元素和子元素的 `font-size` 设置某个值，如 `1.2em`，假设父元素继承得到的字体大小原本是 10px，应用样式后，父元素字体大小变成了 12px，而子元素则变成了 14.4px。一份代码中，相同写法的尺寸的效果可能处处不一样。可想而知，如果大量使用 em，很容易使得代码混乱难懂，所以程序员不会经常使用它。

相比之下，rem 显示出某种统一性。由于仅依赖于 html 根节点的字体大小，因此代码中任何地方的 `2rem` 都绝对是根元素 2 倍大小，没有使用 em 时的“惊喜”。

<!--more-->

## 使用 rem 切图

这是一种简单粗暴但是的确好用的屏幕适配方法。注意，**前提条件是不对页面高度做出限制**，也就是说这种适配方法仅能适应屏幕宽度的变化；如果还需要适配整屏高度，那么单纯使用 rem 可能无法完成。

简单来说，设计师傅出图时使用某款机型尺寸，如 iphone6。切图时我们先完全按照设计图中的像素尺寸进行，chrome 的设备模拟器也设置为 iphone6 的视图。

这样一来，如果切换到其他宽度的手机屏幕，或者将设备横屏，都会出现布局混乱的问题。

### 单位换算

接下来进行一个操作：将所有使用 `px` 的单位，统一换算到 `rem` 单位（方法见后文）。iphone6 的 webview 默认 html 字体大小是 16px，所以如果 CSS 中某元素是 32px 的宽度，那就换算为 2rem，并以此类推。iphone6 的屏幕宽度是 375px，假设使用了一个占据屏幕一半宽度的 div 元素，原本的 CSS 是 `width: 187.5px`，那么换算后是 `width: 11.71875rem`，保持了原始的尺寸。

不过你应该发现了，虽然现在使用的是 rem 布局，但是换到不同宽度的屏幕，并不能保证这个 div 还是占据屏幕宽度的一半。例如 chrome 设备模拟器中的 Pixel2 手机，宽度为 411px，而 html 默认 font-size 还是 16px。此时原本的 `width: 11.71875rem` 计算得到的实际宽度只是 iphone6 屏幕宽度的一半 187.5px，并非期待的 Pixel2 屏宽的一半 205.5px。

下一步是 rem 适配思路的关键：调整 html 元素 font-size 的大小。

### 调整 html 元素的 font-size

想要让 iphone6 上的布局效果完美切换到 Pixel2，就需要让各个元素等比例放大，倍数是 `411 / 375`。所有单位都是基于 rem，也就是基于 html 元素 font-size。直接让 html 的 font-size 放大这个比例，不久就可以使得所有元素同时放大了吗。

所以，在页面加载时，加入下面这段代码：

```JavaScript
var count = 23.4375;
document.documentElement.style.fontSize = document.documentElement.clientWidth / count + 'px';
```

代码中的 count 是从 iphone6 的屏宽除以默认字号计算得来：`375 / 16`。也就是说以 iphone6 的屏幕宽度，可以放下 count 个默认文字。

运行代码后，等效于设置 `Pixel2 font-size *= (Pixel2 width / iphone6 width)`。

或者换种理解：设置 font-size 后，屏幕宽度就可以容纳 count 个字体，也就是说屏幕宽度成了 `count rem`。如果使用 2rem 作为元素宽度，那么这个元素就占用了屏幕的 `2 / count`，这个比例在任何宽度的设备上都一样。

有了对 html 默认字号的修改，理论上就可以无忧无虑地使用 rem 单位进行布局了。任何屏幕上所见的布局都是 iphone6 上的布局等比例缩放的效果。

最后为 window 的 resize 进行事件绑定，窗口尺寸变化时重新设置字号。这样，在旋转屏幕时布局就能自适应屏幕尺寸变化了。

## 我遇到的兼容性问题

rem 虽好，但是在使用的细节上还要留意。比如最近我遇到了一次布局混乱的诡异现象。出现在 Android 的 hybrid 应用上，也就是原生应用的内嵌 webview 组件中。

这个问题一开始并不是百分之百复现，在某些手机上偶尔出现打开 webview 时，界面布局会混乱，具体表现为文字元素全部消失，只留下一张张充满屏幕宽度的图片从上到下依次排布。仔细排查后，终于定位到是 html 元素的 font-size 问题：它居然是 0。

再次排查，发现问题来自于 `document.documentElement.clientWidth`。

某些手机在打开 webview 时，一开始获取 html 元素的 clientWidth 是没有值的，导致代码将 html 的 font-size 设置成了 0。那么布局出错就是必然的了。至于为什么图片还能显示，是因为布局中使用了百分比设置图片大小，如果也使用 rem 设置图片尺寸，图片也会消失。

虽然定位问题花了一番功夫，解决起来还是很容易的，使用兼容的写法获取屏幕宽度就好了：

```JavaScript
var width = document.documentElement.clientWidth || document.body.clientWidth || window.innerWidth;
document.documentElement.style.fontSize = width / count + 'px';
```

## sass 函数的应用

前面提到需要将 CSS 中的 px 单位换算成 rem 单位。通常的做法是在 webpack 中配置一个 loader，编译过程中自动匹配 px 单位，然后按照设定的比例换算。

实际上，如果项目中使用了 sass （一般使用更贴近原生 CSS 语法的 scss 的写法） 代替 CSS，那么有一种更巧妙的方法：使用 sass 函数。

定义尺寸换算函数如下：

```scss
@function rem($size) {
   @return $size / 16 * 1rem;
}
```

使用时，直接利用该函数将 px 单位的数值写入，即可自动换算为 rem 单位：

```scss
.foo {
    width: rem(20);
}
```

参考资料：https://codepen.io/jelleverzijden/pen/xGYZYV?editors=1100