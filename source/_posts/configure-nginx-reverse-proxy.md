---
title: 配置 Nginx 反向代理 Apache 服务器
date: 2017-12-16 22:35:02
tags: [website,Nginx,Apache]
---

本篇博客系转载，有删改，原文章：[前nginx后Apache＋Node反向代理](http://www.cnblogs.com/dolphinX/p/4068857.html) 。



## 场景

有三个网站：

1. localhost:8080
2. localhost:9000
3. localhost:8000

使用 nginx 设置，都使用80端口，通过不同的二级域名进行访问：

1. 当访问 `xxx.domain.com` 的时候代理到 `localhost:8080`
2. 当访问 `yyy.domain.com` 的时候代理到 `localhost:9000`
3. 当访问 `zzz.domain.com` 的时候代理到 `localhost:8000`

<!--more-->

## Apache 配置各站点

> 注：这一步，由于我真实环境中只有一个使用 Apache 作为服务器的站点，因此配置仅仅修改了 `/etc/httpd/conf/httpd.conf` 文件，监听 4000 端口，并修改字段了 `AllowOverride` 为 `All`。

### 修改 httpd.conf 文件

我在`centos`服务器上使用  yum 包管理工具安装的 Apache 配置位于

```
/etc/httpd/conf/httpd.conf
```

设置三个虚拟主机，修改配置文件，去掉加载虚拟主机配置的注释 `#`

```
Include /private/etc/apache2/extra/httpd-vhosts.conf
```

监听对应的端口（不要监听80了，一会儿交给nginx）

```
Listen 8000
Listen 8080
Listen 9000
```

### 修改httpd-vhosts.conf

虚拟主机的配置文件在

```
/etc/apache2/extra/httpd-vhosts.conf
```

添加虚拟主机节点，类似这样来三个

```
 <VirtualHost *:8080>
          DocumentRoot "/path"
          ServerName xxx
          <Directory "/path">
          Options Indexes FollowSymLinks
          AllowOverride All
          Order deny,allow
          Allow from all
          </Directory>
 </VirtualHost>
```


## 修改 hosts 文件

如果服务器在本地，我们还需要把域名配置到localhost，hosts的配置文件在

```
/etc/hosts
```

简单添加一下我们的三个主机

```
localhost xxx.domain.com yyy.domain.com zzz.domain.com
```



## 修改 nginx 配置文件

新建单独配置文件于 `/etc/nginx/conf.d/new_file.conf`

配置虚拟主机 × 3：

```nginx
server {
        listen 80;
        listen [::]:80;
        server_name xxx.domain.com;
        location / {
            proxy_pass http://0.0.0.0:8080;
            proxy_set_header Host $host;
        }
}
```

`proxy_set_header Host $host` 可以使url始终保持配置的 `xxx.domain.com` 等。



最后重启 Nginx 和 Apache 即可。
