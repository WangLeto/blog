---
title: Android studio 离线安装 SDK 源码
date: 2018-06-16 22:03:56
tags: [Android]
---

用了 Android studio 推荐的 SparseBooleanArrary 这个类，感觉像是被拉上了贼船（其实是自己水平太低吧喂），想要找源码，居然下载不了（可能是设置代理起到了反效果）。

漫长的等待后，发现正在从 `https://dl.google.com/android/repository/sources-26_r01.zip` 这个网址下载源码文件，赶快用 chrome 尝试下载，没想到速度飞起。（等等，我懂了，是因为现在有 IPV6 的原因，浏览器这部分流量通过 hosts 修改后的 ipv6 渠道走的，所以网速快）。

<!--more-->

拿到了压缩文件，名字是 `sources-26_r01.zip`，里面一个 `src` 文件夹，在里面就是像这样的：

![](http://qiniu1.letow.top/snipaste%202018.06.16-21.57.jpg)

我思索许久，决定将它解压到 SDK 所在目录下刚刚出来的 `AndroidSDK\sources\android-26` 这个位置。但是文件结构是什么样的呢？这个问题从高中玩手游放置数据包开始就深深困扰着我，每次移动都提心吊胆担心一失足成万古恨，又在苦苦等待中心如刀绞。然而现在毕竟不同了，依然是个大人的我可以在电脑移动文件的时候玩手机。

终于试出来了，将源码包 `src` 下的文件内容直接放置在 `android-26` 里是正确的做法，我们可以愉快地看源码了（并不会。
