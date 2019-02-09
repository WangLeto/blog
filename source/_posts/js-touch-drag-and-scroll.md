---
title: 一步步实现网页图片的手势拖拽与缩放
date: 2019-02-01 08:43:51
tags: [JavaScript]
mathjax: true
---

可以点击查看 [在线演示](https://link.juejin.im?target=http%3A%2F%2Fblog.letow.top%2Fhtml-drag-scale-demo%2F)，以及 [完整代码](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FWangLeto%2Fhtml-drag-scale-demo%2Fblob%2Fmaster%2Findex.html)。

本文也发布在了掘金上，链接：https://juejin.im/post/5c52b2b26fb9a049fb44168e。

## CSS transform

首先，需要了解 CSS3 的 `transform` ，用 `transform` 进行元素的变换，这是实现的关键。

`transform` 最常用的形式像这样：

```CSS
// 放大 2 倍
transform: scale(2);

// 向左平移 100px
transform: translate(100px);

// rotate，skew，perspective 等其他变换
```

其实，上面的写法可以算作 CSS 提供的语法糖。了解计算机图形学的同学可能知道，计算机完成图像变换实际上使用的实现是矩阵。

<!--more-->

如果使用以下 JavaScript 代码更改并查询一个 div 元素的 CSS transform 属性：

```JavaScript
document.querySelector('div').style.transform = 'scale(1)';
console.log(window.getComputedStyle(document.querySelector('div'), null).getPropertyValue('transform'));

// 输出 "matrix(1, 0, 0, 1, 0, 0)"
```

可以看到此时 transform 的值并不是“scale(1)”，而是一个矩阵表示。此处 matrix 中的 6 个参数，对应了 2D 仿射变换矩阵中起作用的 6 个值（完整的是 3*3 矩阵，但是有 3 个参数是固定的）——不过这跟本文的实现没有太大关系。为了简单起见，只需知道在 matrix 用到的参数即可。


![简单的matrix参数解释](http://qiniu1.letow.top/snipaste%202019.01.30-18.03.jpg)


**但这绝对不是 matrix 完整的正确用法**

如果想要多了解一些关于变换矩阵的知识，请搜索“仿射变换”。

知乎上有一个很好的入门回答：[如何通俗地讲解「仿射变换」这个概念？ - 马同学的回答](https://link.juejin.im?target=https%3A%2F%2Fwww.zhihu.com%2Fquestion%2F20666664%2Fanswer%2F15740056)。

如果不愿意写矩阵形式，也可以将其等价地写成：

```CSS
transform: translate(200px, 100px) scale(3);
```

注意，**书写顺序决定了变换顺序，不可以将 scale 放置在 translate 之前**：[Is a css transform matrix equivalent to a transform scale, skew, translate](https://link.juejin.im?target=https%3A%2F%2Fstackoverflow.com%2Fa%2F27475543%2F7526989)。

## Touch 事件

在进行实现之前，需要先了解一点触摸事件的处理。详见 [触摸事件](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FTouch_events)。

这里简单介绍一下相关的事件：

`touchstart`：触摸事件开始，表示一个触摸点开始接触。可以通过传入对象获取 `touches`，即一 个 [`TouchList`](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FTouchList) 对象，里面含有当前所有的接触点，即 [touch](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FTouch) 对象。下面 2 个事件传入参数相同。

`touchmove`：触摸点移动。

`touchend`：触摸事件结束，表示一个触摸点离开。

`TouchList`：是一种“类数组”对象，也就是和函数中拿到的 `arguments` 相似，不是数组，但是含有 `length` 属性，以及 `0`、`1` 这样的 key 值 ，可以通过 `Array.prototype.slice` 转为数组。也可以使用 `touches['0']` 这样的语法直接从 touches 中取出触摸点对象。

### 需要了解

- 触摸事件传入的参数是组合对象，因此如果使用了 React 框架，最好不要向异步方法，如 setTimeout、Promise、async / await 区域中传递该参数。可以先使用变量获取需要使用的值，再进行传递。如果一定要传入，可以使用 `e.persist()` 将对象持久化。

- 区别于点击事件，无法从触摸事件中直接获得 offsetX offsetY。因此需要自己计算这两个值。

  [Why don't Touch objects have offsetX and offsetY properties like MouseEvent does? --The elephant in the room is Apple.](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fw3c%2Ftouch-events%2Fissues%2F62%23issuecomment-204888619)

## 拖拽的实现

网上找 DOM 元素拖拽，通常的做法是使用相对定位与 top、left 属性。但是结合缩放事件，本文将使用 transform 进行实现。

不过无论具体实现方式如何，移动元素的思想都是一致的：先计算两次 move 事件中的触摸位移，然后将这段位移应用到目标上。

HTML 部分：

```html
<head>
  <meta charset="UTF-8">
  <!--一些方便实现的声明-->
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no, minimum-scale=1.0, maximum-scale=1.0, user-scalable=0" />
  <title>Touch</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      width: 100%;
      // 禁用页面拖动刷新
      overscroll-behavior: contain;
    }
    .board {
      width: 100%;
      height: 100%;
    }
    .board img {
      width: 260px;
    }
  </style>
</head>

<body>
  <div class="board">
    <!--盗了少数派的图-->
    <img src="https://cdn.sspai.com/article/86c69914-4545-bc1c-1310-2975d4fe8d6b.jpg?imageMogr2/quality/95/thumbnail/!700x233r/gravity/Center/crop/700x233" alt="">
  </div>
</body>
```

JavaScript 部分：

```JavaScript
let img = document.querySelector('img');

// 查询 DOM 对象的 CSS 值
const getStyle = (target, style) => {
  let styles = window.getComputedStyle(target, null);
  return styles.getPropertyValue(style);
};

// 获取并解析元素当前的位移量
const getTranslate = (target) => {
  let matrix = getStyle(target, 'transform');
  let nums = matrix.substring(7, matrix.length - 1).split(', ');
  let left = parseInt(nums[4]) || 0;
  let top = parseInt(nums[5]) || 0;
  return { left: left, top: top };
};

// 记录前一次触摸点的位置
let preTouchPosition = {};
const recordPreTouchPosition = (touch) => {
  preTouchPosition = {
    x: touch.clientX,
    y: touch.clientY
  };
};

// 应用样式变换
const setStyle = (key, value) => { img.style[key] = value; };

// 添加触摸移动的响应事件
img.addEventListener('touchmove', e => {
  let touch = e.touches[0];
  let translated = getTranslate(touch.target);
  // 移动后的位置 = 当前位置 + （此刻触摸点位置 - 上一次触摸点位置）
  let translateX = translated.left + (touch.clientX - preTouchPosition.x);
  let translateY = translated.top + (touch.clientY - preTouchPosition.y);

  let matrix = `matrix(1, 0, 0, 1, ${translateX}, ${translateY})`;
  setStyle('transform', matrix);

  // 完成一次移动后，要及时更新前一次触摸点的位置
  recordPreTouchPosition(touch);
});

// 开始触摸时记录触摸点的位置
img.addEventListener('touchstart', e => { recordPreTouchPosition(e.touches['0']); });
```

## 缩放的实现

### 初步实现

要进行缩放，就要知道缩放的倍数。进行缩放是双指的动作，有 2 个触摸点，而将触摸点之间的距离变化对应到缩放倍率的变化，就可以实现双指缩放的效果。要得知缩放的变化，思路跟移动一致，也是要记录上次的触摸点距离。然后就可以计算现在的缩放倍率。

```JavaScript
let scaleRatio = 1;
// 从变量名就知道它的用途与用法
let preTouchesClientx1y1x2y2 = [];
img.addEventListener('touchmove', e => {
  let touches = e.touches;
  if (touches.length > 1) {
    // 即便同时落下 10 个手指，我们只取前 2 个就好
    let one = touches['0'];
    let two = touches['1'];
    const distance = (x1, y1, x2, y2) => {
      let a = x1 - x2;
      let b = y1 - y2;
      return Math.sqrt(a * a + b * b);
    };
    // 新的缩放倍率 = （当前指间距离 ÷ 之前指间距离）× 之前缩放倍率
    // 没有在 touchstart 中记录最初的双指位置，计算会得到 NaN，对结果直接取 1
    scaleRatio = distance(one.clientX, one.clientY, two.clientX, two.clientY) / distance(...preTouchesClientx1y1x2y2) * scaleRatio || 1;
    let matrix = `matrix(${scaleRatio}, 0, 0, ${scaleRatio}, ${translateX}, ${translateY})`;
    setStyle('transform', matrix);
    // 及时更新双指位置信息
    preTouchesClientx1y1x2y2 = [one.clientX, one.clientY, two.clientX, two.clientY];
  }
});
img.addEventListener('touchstart', e => {
  let touches = e.touches;
  // 双指同时落下也是有先后顺序的，当发现多指触摸时进行记录
  if (touches.length > 1) {
    let one = touches['0'];
    let two = touches['1'];
    preTouchesClientx1y1x2y2 = [one.clientX, one.clientY, two.clientX, two.clientY];
  }
  recordPreTouchPosition(touches['0']);
});
```

现在已经实现了基本的缩放功能，但是好像哪里不太对……为什么感觉缩放效果不是从手指中传出的呢？似乎不管在哪里操作，都是从图片中心开始的。

### transform-origin

简单介绍一个 CSS 属性：`transform-origin`，详细介绍见 [MDN](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2Ftransform-origin)。

此属性规定元素基点，也就是是应用变换的原点。

```CSS
// 元素基点设置为 (50px, 50px)，是元素上的相对坐标
transform-origin: 50px 50px;
```

当图形变换只有位移时，transform-origin 不会有什么影响。但是对于旋转和缩放属性来说，元素基点是重要的属性。

而默认的 transform-origin 值是 `50% 50%`，也就是元素正中心。这也就是为什么每次进行缩放操作，都感觉缩放从图片中心点传来。

如果想要感受缩放效果从手指开始，就要将 transform-origin 设置在双指中间的位置；或者，通过位移的计算，模拟出 origin 的变化。本文采用前一种更直观的思路。

### 获取触摸的 offset

实际上，不管元素被变换成了什么形状，设置 origin 时都是采用相对元素变换前的偏移量。之前提到过 touch 事件中并没有触摸点相对于元素的 offset 值，因此需要自己来计算。

```JavaScript
// 计算相对缩放前的偏移量，rect 为当前变换后元素的四周的位置
const relativeCoordinate = (x, y, rect) => {
  let cx = (x - rect.left) / scaleRatio;
  let cy = (y - rect.top) / scaleRatio;
  return {
    x: cx,
    y: cy
  };
};
```

其实就是 `(所选的屏幕位置 - 元素的屏幕位置) / 缩放比例`，并不困难。rect 可以直接使用 `getBoundingClientRect` 函数获得。（之前误以为 `getBoundingClientRect` 获取的位置不正确，自己实现了一下![img](https://user-gold-cdn.xitu.io/2019/1/31/168a309fac43cb8c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)。思路是利用父定位元素累加 offset，**喜欢挑战的朋友务必自己尝试一下**，有意外惊喜哦）

至于“所选的屏幕位置“，取双指中点的位置。这里选取 clientX 和 clientY 值计算，即距离浏览器的偏移量。

```JavaScript
// 记录变换基点
let scaleOrigin = {};
img.addEventListener('touchmove', e => {
  let touches = e.touches;
  if (touches.length > 1) {
    let one = touches['0'];
    let two = touches['1'];
    const distance = (x1, y1, x2, y2) => {
      let a = x1 - x2;
      let b = y1 - y2;
      return Math.sqrt(a * a + b * b);
    };
    scaleRatio = distance(one.clientX, one.clientY, two.clientX, two.clientY) / distance(...preTouchesClientx1y1x2y2) * scaleRatio || 1;
    // 移动基点
    let origin = relativeCoordinate((one.clientX + two.clientX) / 2, (one.clientY + two.clientY) / 2, img.getBoundingClientRect());
    scaleOrigin = origin;
    setStyle('transform-origin', `${origin.x}px ${origin.y}px`);
      
    let matrix = `matrix(${scaleRatio}, 0, 0, ${scaleRatio}, ${translateX}, ${translateY})`;
    setStyle('transform', matrix);
    preTouchesClientx1y1x2y2 = [one.clientX, one.clientY, two.clientX, two.clientY];
  }
});
```

似乎完成了？上手试一下。emmm……多操作一下就能发现，每次缩放离手后再次进行缩放，目标对象完全不受控制，甚至会瞬移。

### 修改 transform-origin 带来的问题

稍微思考，我们就能发现问题所在（不存在的，我 debug 好久）：对于已经应用过缩放（或旋转）的元素，修改 origin 位置时，会产生位置的突然变化。

具体是怎么回事呢？其实这是一个高中数学就能够解释的问题。

#### 一点高中数学



![元素基点位于原点](http://qiniu1.letow.top/snipaste%202019.01.31-12.19.jpg)



以上是元素基点位于原点的情况。此时缩放倍率为 2，缩放前的点 A 坐标为 (3, 2)，变换后 A' 为 (6, 4)。

那么，如果 origin 不在原点呢？将 origin 移动到 (1, 1) 时，情况如下：



![元素基点位于 1, 1](http://qiniu1.letow.top/snipaste%202019.01.31-16.20.jpg)



可以看到，A' 点的坐标变为了 $(5, 3)$。其实现在从数值上已经可以看出一点端倪了，但是让我们来做一点抽象归纳。

首先，将基点 O 设为 $O = (o_x, o_y)$。此时如果点 A 坐标为 $(x, y)$ ，缩放倍率为 **s**。用向量来表示点 A 到基点的距离就是：

$$
\overrightarrow{OA}=(x-o_x,y-o_y)
$$

那么此时点 A' 到基点的距离正是 $\overrightarrow{OA}$ 的 **s** 倍：

$$
\overrightarrow{OA'}=(s\cdot(x-o_x),s\cdot(y-o_y))
$$

点 A' 的坐标即 $\overrightarrow{OA'}$ 的值加上点 O 的坐标：

$$
A'=(s\cdot(x-o_x)+o_x,s\cdot(y-o_y)+o_y)
$$

如果我们移动基点 O，现在点 O 的坐标变为了：$O=(o_x',o_y')$。我们并没有改变坐标系参考点，点 A 的坐标仍是 $(x, y)$，此时点 A 到基点 O 的距离为：

$$
\overrightarrow{OA}=(x-o_x',y-o_y')
$$

而新由点 A 变换得到的点 A'' 到基点 O 的距离 $\overrightarrow{OA}$ 的 **s** 倍）就变成了：

$$
\overrightarrow{OA''}=(s\cdot(x-o_x'),s\cdot(y-o_y'))
$$

此时点 A'' 的坐标是 $\overrightarrow{OA''}$ 加上点 O 的坐标：

$$
A''=(s\cdot(x-o_x')+o_x',s\cdot(y-o_y')+o_y')
$$

也就是说，将基点 O 从 $(o_x, o_y)$，记增量为 $(\Delta{o_x},\Delta{o_y})$，导致了点 A 的变换结果，从 $(s\cdot(x-o_x)+o_x,s\cdot(y-o_y)+o_y)$ ，变成了$(s\cdot(x-o_x')+o_x',s\cdot(y-o_y')+o_y')$ 。计算 A 点缩放后的图像因为元素基点移动而变化了的值，也就是点 A'' 到点 A' 的距离：

<p>
$$
\begin{equation} \begin{aligned} \overrightarrow{A'A''}&=(s\cdot(x-o_x')+o_x',s\cdot(y-o_y')+o_y')-(s\cdot(x-o_x)+o_x,s\cdot(y-o_y)+o_y)\\\\&=(s\cdot(o_x-o_x')+o_x'-o_x,s\cdot(o_y-o_y')+o_y'-o_y)\\\\&=((1-s)\cdot\Delta{o_x},(1-s)\cdot\Delta{o_y}) \end{aligned} \end{equation}
$$
</p>

可以带入上面图片中的真实坐标值进行验证，结果是符合预期的。

### 消除修改 origin 位置带来的影响

这样就解释得通了，在**双指落下的一瞬间，origin 坐标变化了 $(\Delta{o_x},\Delta{o_y})$，而取任变换结果上的一点 X ，变化了 $((1-s)\cdot\Delta{o_x},(1-s)\cdot\Delta{o_y})$**。观察发现，这个值与点 X 自身的坐标没有任何关系，是 origin 移动距离决定的一个“定值”；也就是说，元素上的所有的点，同时产生了这端位移的变换效果。反映到界面上来，就是双指接触元素的瞬间，元素立刻“瞬移”一下。而随着手指不断改变位置，origin 不断被重设，于是造成了缩放元素完全不受控制的局面。

要消除修改 origin 带来的负面影响，有 2 点需要做：

- 修改 origin 的同时修改位移，使得目标点的位移效果被抵消
- 减少 origin 的修改次数，可以减少不必要的计算量

#### 进行修正

之前的计算中，我们得到了元素发生了 $((1-s)\cdot\Delta{o_x},(1-s)\cdot\Delta{o_y})$ 的平移，于是只需要在修改 origin 位置的同时，将位移量提前减去这个值即可。另外，我们将 origin 的修改频率从每个 touchmove 事件进行一次，减少到“完整的一次缩放交互”进行一次。

```JavaScript
// 增加 originHaveSet 全局变量，每次设置 origin 位置后设为 true
img.addEventListener('touchmove', e => {
  // ...
  if (!originHaveSet) {
    originHaveSet = true;
    // 移动视线中心
    let origin = relativeCoordinate((one.clientX + two.clientX) / 2, (one.clientY + two.clientY) / 2, 
  img.getBoundingClientRect());
    // 修正视野变化带来的平移量，别忘了加上之前已有的位移值啊！
    translateX = (scaleRatio - 1) * (origin.x - scaleOrigin.x) + translateX;
    translateY = (scaleRatio - 1) * (origin.y - scaleOrigin.y) + translateY;
    setStyle('transform-origin', `${origin.x}px ${origin.y}px`);
    scaleOrigin = origin;
  }
  // ...
});
img.addEventListener('touchstart', e => {
  let touches = e.touches;
  if (touches.length > 1) {
    // ... 开始缩放事件时，将标志置为 false
    originHaveSet = false;
  } //...
});
```

这时再看一下效果，不禁流下了感动的泪水。终于能够正常缩放了，这完美的跟手效果，这顺滑的缩放体验……

稍等，缩放后拿开手指，为什么图片有时候还是会跳动啊。

### 一点尾巴

仔细检查一下代码，定位发现是单手 touchmove 的问题：双手缩放后移开，有时会触发单手的一个 touchmove 逻辑；而之前拖拽实现时用到的上一次接触点位置并没有及时更新，导致了计算出的图片移动距离与实际不符。

那么在 touchend 与 touchcancel 中加入一个更新逻辑即可：

```JavaScript
img.addEventListener('touchend', e => {
  let touches = e.touches;
  if (touches.length === 1) {
    recordPreTouchPosition(touches['0']);
  }
});
// touchcancel 同样
```

完整的代码可以在我的 github 上获得：[html-drag-scale-demo](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FWangLeto%2Fhtml-drag-scale-demo%2Fblob%2Fmaster%2Findex.html)。
