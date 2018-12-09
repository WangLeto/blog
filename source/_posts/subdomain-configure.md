---
title: 使用 nginx 配置二级域名
date: 2017-12-10 22:25:07
tags: [website,Nginx]
---

说来惭愧，这个博客之前搭建完成后，始终有一个问题困扰着我：为什么浏览器访问进行页面跳转时，URL 总是同一个，也就是首页地址 `blog.letow.top` ？

后来经过老师指点，才发现自己在胡乱配置 nginx 的道路上走了太远……对于虚拟主机、DNS 解析配置都不够了解。

经过一番查找，终于在一篇 Apache 的配置讲解博客中找到了自己需要的方案：[二级域名共享主机IP和端口](http://blog.csdn.net/Pc620/article/details/51314125) 。

<!--more-->

## 所要处理的情形

两个用于站点的文件夹位于完全不同的路径，需要使用不同的二级域名可以各自访问对应的站点页面。

## 方法
### 配置 nginx

“虚拟主机”的用法很灵活，只需要在一份配置 nginx 的 test.conf 文件中指定站点文件所在的文件夹，指定站点域名即可，建议该文件位于 `/etc/nginx/conf.d/` 目录里。虚拟主机就是配置文件中的 server 与大括号后的字段。

```nginx
server {
        listen 80;
        listen [::]:80;
        root /root/blog/public;            
        index index.html;
        server_name blog.letow.top;
        location / {
        }
}
```

- root 指定了站点文件夹的位置
- server_name 是 nginx 接收解析的域名

对于另一个站点，再单独写一个类似的配置文件。



最后，在 nginx 默认的配置文件 `/etc/nginx/nginx.conf` 中的 http 字段结束前，加上一句 `include /etc/nginx/conf.d/*.conf;`，即可向 nginx 加载这两份配置文件。

要立即使用这份配置，运行命令：

```shell
nginx -s reload
```



### 更改 DNS 解析记录

还有一个很重要的步骤就是配置 DNS 解析。

![](http://qiniu1.letow.top/subdomain-configure-2017-12-11-00-14-19.png)

只需要添加一条 A 类型解析就可以了，主机记录填写 * ，这样所有的二级域名都被解析到主机，接下来的工作由 nginx 完成。



这么简单的配置，实在是花了一番功夫去找……

![](http://qiniu1.letow.top/nginx-subdomain-2017-12-47-02-47.jpg)
