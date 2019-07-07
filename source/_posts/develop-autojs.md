---
title: 玩一下 auto.js
date: 2019-07-07 21:00:02
tags: Android
---

以前用过一段时间 auto.js 的蚂蚁森林脚本，后来经常出问题就放弃了。最近突然手痒，忍不住想要自己写脚本来玩一下。个人感觉爬虫自动化什么的是写代码获得成就感最快的途径了。

<!--more-->

## 自动更新文件

编程的一大难题是环境问题，环境搭建过程中的困难往往击退了一批又一批不够坚定的新人（比如不断放弃的我 ==）。

其实 auto.js 有一个 vs code 插件，可以在连接局域网的情况下直接推送并运行脚本的，但是有 2 个问题：

1. 不知道是自如的 wifi 路由器质量问题还是我的手机真的不行了，wifi 连接 adb 的总是掉线。而神奇的 Mac 无法同时连接 wifi 和提供 wifi 热点（苹果工程师傲慢指数 +1）。
2. 通过这种方式推送运行的脚本有时会丢失部分权限，比如无法通过 intent 唤起支付宝。就很迷。

因此，只能通过 USB 连接 adb 后，使用 adb push 命令更新文件，然后手动点击运行脚本。想了想，好像可以用 node 自动监视文件变动的能力，自动执行 adb push 呀！

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

……额，仔细想了想，似乎也没有节约太多工作量，但是还是会因为节约了脑子而有点高兴诶。😝

---

目前还在熟悉 auto.js 的接口，估计最后写出来的也只是仅仅能跑起来的面条代码……

to be continue...