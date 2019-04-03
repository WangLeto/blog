---
title: 重现 hexo 博客
date: 2019-04-04 02:53:28
tags: [其他]
---

刚刚提到了换用 Linux 系统，为了更新博客又在 Linux 上 clone 了一份。不过遇到了点问题，特此记之。

1. 克隆博客项目，安装 hexo-cli。

2. `hexo g` 跑不起来，回想起是主题的问题。进入主题目录 `themes`，克隆 `hexo-theme-next` 项目到 `next` 目录。

   稍微说明一下：next 主题官方也是使用 git 管理的，所以出现了两个 git 项目嵌套的情况。因为一开始没有设置好子项目，所以无法同时保留内部嵌套的 next 项目。按照 next 官方说明的方法，将主题的配置文件放置于 `blog/source/_data/next.yml`，这样配置文件不会丢失。

3. 此时运行 `hexo g` 不报错了，但是 `hexo s` 启动服务器后还是一片空白。打开 chrome devtools 看一下，发现跟 `fancybox` 相关的文件报错了。原来 next 主题启用 fancybox 后没办法自动安装依赖。进入主题目录 `themes/next/source/lib/` 下，克隆 fancybox 3 的仓库就行了。