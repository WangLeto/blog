---
title: 玩一下 auto.js
date: 2019-07-07 21:00:02
tags: Android
---

以前用过一段时间 auto.js 的蚂蚁森林脚本，后来经常出问题就放弃了。最近突然手痒，忍不住想要自己写脚本来玩一下。个人感觉爬虫自动化什么的是写代码获得成就感最快的途径了。

<!--more-->

## 自动更新文件

编程的一大难题是环境问题，环境搭建过程中的困难往往击退了一批又一批不够坚定的新人（比如不断放弃的我 ==）。

### 使用官方插件

其实 auto.js 有一个 vs code 插件，可以在连接局域网的情况下直接推送并运行脚本的，但是有 2 个问题：

1. 不知道是自如的 wifi 路由器质量问题还是我的手机真的不行了，wifi 连接 adb 的总是掉线。而神奇的 Mac 无法同时连接 wifi 和提供 wifi 热点（苹果工程师傲慢指数 +1）。
2. 通过这种方式推送运行的脚本有时会丢失部分权限，比如无法通过 intent 唤起支付宝。就很迷。

因此，只能通过 USB 连接 adb 后，使用 adb push 命令更新文件，然后手动点击运行脚本。想了想，好像可以用 node 自动监视文件变动的能力，自动执行 adb push 呀！

### 使用 fs.watchFile

Google 一番，有了下面的脚本：

```javascript
const fs = require('fs');
const { exec } = require('child_process');

fs.watchFile('main.js', { interval: 500 }, () => {
  console.log('文件变动');
  exec('adb push main.js /storage/emulated/0/1');
});
```

忘了说明，文件夹下的文件结构很简单：

```
.
├── auto-push.js
└── main.js

0 directories, 2 files
```

只需要检测到 main.js 后自动推送该文件到手机的 `/sdcard/0/1` 路径里就 OK 了。

### 结合 auto.js 动态执行文件的能力

仅仅是更新脚本是不够的，引入 auto.js 里面执行的脚本需要事先导入，而导入后的文件是不能跟随文件系统实时更新的。

找了一下，发现 auto.js 提供了执行系统文件脚本的方法，bong！

```javascript
engines.execScriptFile('/sdcard/1/main.js');
```

这样就 OK 啦！

注意这里 auto.js 跟我们使用 adb 访问文件系统的路径不完全一样，猜测里面应该有一些 Android 底层的原理。🤔 算了，我只是个页面切图仔，管那么多干嘛呢。（这就是我跟大佬的差距吧……）

仔细想想，似乎也没有省掉太多工作量，但是还是会因为节约了（来回倒腾文件的不必要的）脑力而有点高兴诶。😝

## 最后的脚本

用了几个小时，总算攒出来一份无法复用的脚本。😂

其中 `take.png` 和 `help.png` 分别对应了蚂蚁森林中好友能量和帮助收取的标识，用于图片匹配（以前瞅了眼 auto.js 的源码，似乎用的是 openCV 进行的图片模式匹配）。

```javascript
let actions = {
  scrollY (from, to) {
    gesture(500, [300, from], [300, to]);
  },
  scrollYBy (from, offset) {
    this.scrollY(from, from - offset);
  },
  clickTarget (target) {
    let bound = target.bounds();
    click(bound.centerX(), bound.centerY());
  }
};

let _toast_ = toast;
toast = function(message){
  _toast_(message);
  sleep(2000);
}

!requestScreenCapture() && exit();

function getProperEntry () {
  const find = path => findImage(captureScreen(), images.read(path));
  return find('/sdcard/1/take.png') || find('/sdcard/1/help.png');
}

function goIntoEntry () {
  let point = getProperEntry();
  point && click(point.x, point.y + 50);
  return !!point;
}

const noMoreProperEntry = () => !getProperEntry();
const jobDone = () => { 
  if (!noMoreProperEntry()) {
    return false;
  }
  let noMoreBtn = text('没有更多了').findOne(200);
  if (noMoreBtn && noMoreBtn.bounds().top < 1850) {
    return true;
  } else {
    return false;
  }
};

function openAlipayForest () {
  app.launch('com.eg.android.AlipayGphone');
  waitForActivity('com.eg.android.AlipayGphone.AlipayLogin');
  sleep(1500);
  click(140, 540);
  waitForActivity('com.alipay.mobile.nebulacore.ui.H5Activity');
  sleep(2000);
};

function clearBubbles () {
  const clickAll = range => {
    let btns = range.find();
    btns.forEach(e => {
      actions.clickTarget(e);
    });
  };
  clickAll(className('Button').boundsInside(70, 380, 1000, 1000));
  clickAll(textStartsWith('收集能量'));
  sleep(2000);
}

openAlipayForest();
// 清空自己的能量泡泡
clearBubbles();
// 打开全部好友列表
while (!text('查看更多好友').findOne(200)) {
  actions.scrollYBy(1700, 400);
}
text('查看更多好友').findOne(500).click();
sleep(2000);

// 开始进入并收取
while (1) {
  if (goIntoEntry()) {
    sleep(2000);
    clearBubbles();
    back();
    sleep(1000);
  }
  if (noMoreProperEntry()) {
    if (jobDone()) {
      toast('收完了');
      exit();
    } else {
      actions.scrollYBy(1700, 500);
    }
  }
}
```

![](http://qiniu1.letow.top/0016nEYngy6YYNbeHiD4f&690.jpeg)

代码里有很多地方写的不好，一部分是为了快点写出来没有优化，一部分是因为 auto.js 运行个别代码不完全跟一般 JavaScript 引擎一样（可能跟我自己的手机有关系）。

不管怎么样，auto.js 确实是很优秀的一款 Android 动态脚本引擎。