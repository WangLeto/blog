---
title: ubuntu 系统配置（16.04）
date: 2017-11-04 20:53:36
tags: linux
---

原来在机械硬盘上安装了 ubuntu 16.04 版本，后来感觉不过瘾就又在固态硬盘上又安装了一个。配置得差不多了以后，就考虑着把机械硬盘上的系统删除掉。强行体验了一下 `sudo rm -rf /` 是什么感觉。
事实证明，这是一个非常愚蠢的决定：
- 机械硬盘上的 ubuntu 系统没有被删除干净，这也是意料之中的事情，毕竟没人能举起自己
- 然而真正让我感到意外的是，被我确认过没有挂载的固态硬盘上的 ubuntu，居然被删除得一干二净
  怀着悲愤的心情，我认为不应该浪费了又一次配置系统的机会，于是在固态硬盘重新安装好系统后，我决定记录下这次配置系统的过程。纪念也好，记录也罢，我们开始吧。

<!--more-->

## 更新系统
按理说这里应该潇洒地打出 `sudo apt-get update` 等配套命令，但是考虑到国内堪忧的 ubuntu 软件源速度，即使是在实验室的千兆 ipv6 网络环境下我也不愿轻易尝试，于是我们先:
### 换源
搜索 ubuntu 16.04 对应的国内源，通常选取阿里云。注意一点：ubuntu 16.04 的发行代号是 xenial ，网上流传的部分教程是错误的，而这将引起非常不愉快的体验。

1. 备份系统默认的源

 ```shell
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

2. 更换

 ```shell
sudo gedit /etc/apt/source.list
```

 在编辑器中将内容改为

 ```
deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
deb http://archive.canonical.com/ubuntu xenial partner
deb-src http://archive.canonical.com/ubuntu xenial partner
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse
```

3. 更新软件列表

 ```shell
sudo apt-get update
```

 接下来执行

 ```shell
sudo apt-get upgrade
```

 进行系统更新。

 如果过程中出现了“有几个软件包无法下载，要不运行 apt-get update 或者加上 --fix-missing 的选项再试试？”的提示，只需按照提示执行命令

 ```shell
sudo apt-get update --fix-missing
```

 然后再次执行 upgrade 命令即可。



## 常用工具类软件
为了省心省力，我们需要一些实用行软件。



### 搜狗输入法
这基本是 linux 必装应用，中文输入全靠它。
浏览器搜索“搜狗输入法 linux”，进入官网下载 64 位版本安装包（如果是 32 位电脑当然下载 32 位的安装包）。
下载完成后可以尝试直接打开文件，使用 ubuntu软件 这个系统自带的软件商店安装，但是我亲身尝试经常失败，于是找到下载的安装包，使用命令行进行安装：

```shell
sudo dpkg -i sogoupinyin_xx.xx.xxx_amd64.deb 
```

不巧的是，再次出现报错：“处理软件包 sogoupinyin (--install)时出错： 依赖关系问题 - 仍未被配置”，巴拉巴拉一串，其实只是依赖没有安装而已，执行命令安装依赖：

```shell
sudo apt-get install -f
```

完成后再次执行 dpkg 命令即可完成安装。重启系统，就可以在输入法切换中找到搜狗输入法。关于词库下载、更换皮肤等，不再赘述。

**PS：** 这次安装搜狗输入法意外发现 linux 版本与 Windows 版本的设置项几乎一致了。要知道 Linux 下的搜狗输入法以前是不支持登录账号的，没想到现在可以无缝衔接了，好幸福啊！



### chrome
先安装搜狗输入法而不是 chrome 只是因为给这份文档码字使用默认的输入法过于难受，我可是 chrome 的忠实拥趸～
直接搜索 chrome，进入官网，下载 64 位 deb 安装包，发现 Ubuntu软件 在重启后已经可以正常安装软件了。
然而，想要真正享受到 chrome 跨平台、跨设备的方便，必须有一点点科学上网的手段。

#### 修改 hosts
因为在实验室，有 ipv6 网络很是方便，直接在 github 的一个 ipv6 host 项目中下载 host 更换即可，这是 [链接](https://raw.githubusercontent.com/lennylxx/ipv6-hosts/master/hosts)。
下载得到的如果是 txt 文件，需要把扩展名去掉，只保留“hosts”即可。

替换系统的 hosts：

```shell
sudo cp ./hosts /etc/
```

浏览器尝试，成功登录 Google！
然而，如果此时执行含有 `sudo` 的命令，会发现终端报错：“sudo: 无法解析主机：***”。这是因为 hosts 中找不到本机域名对应的 ip 地址引起的，只需在 hosts 中添加一行记录即可：

```shell
sudo gedit /etc/hosts
```

写入 `127.0.0.1  你的电脑名` 即可（这里“你的电脑名”即报错提示的无法解析主机的名称，这是你在安装 Ubuntu 时位你的电脑所起的名称）。
保存后在终端再次尝试含有 `sudo` 的命令，发现报错消失了。
搞定后不要忘了登录 chrome 哦。



### vim，git

这两款软件绝对是装机必备啊

```shell
sudo apt install git vim
```



### sublime text 3

sublime text 是一款享誉盛名的文本编辑器，开发者实在不应该错过。
不过最近 sublime 的官网似乎墙掉了？可能下面的方法无法正确安装……
#### 方法一
1. 添加 ppa 仓库：

 ```shell
sudo add-apt-repository ppa:webupd8team/sublime-text-3
```

 过程中按下回车进行确认。

2. 更新软件列表

 ```shell
sudo apt update
```

3. 下载安装 sublime text 3

 ```shell
sudo apt install sublime-text-installer
```

#### 方法二
参考这篇教程：[Ubuntu 16.04下Sublime Text 3 的安装和中文配置](http://www.linuxidc.com/Linux/2017-01/139237.htm) 。
借助下面 xx-net 的帮助，可以访问 sublime 的官网了，但是按照官方教程无法正常完成，因此直接使用打包好的二进制程序。

![](http://qiniu1.letow.top/sublime-sample-2017-11-04-20.17.jpg)

1. 下载压缩包

 ```shell
wget https://download.sublimetext.com/sublime_text_3_build_3143_x64.tar.bz2
```

 该 URL 中含有的版本号是在官网下载链接中给出的，应该是当前最新的。当然，如果你能够直接通过浏览器下载该文件，就不需要使用 `wget` 命令了。
 然而终端报错：

 ```
错误: 无法验证 download.sublimetext.com 的由 “CN=GoAgent XX-Net,OU=GoAgent Root,O=GoAgent,L=Cernet,ST=Internet,C=CN” 颁发的证书: 无法本地校验颁发者的权限。要以不安全的方式连接至 download.sublimetext.com，使用“--no-check-certificate”。
```
 那么我们按照提示执行就好了：

 ```shell
wget --no-check-certificate https://download.sublimetext.com/sublime_text_3_build_3143_x64.tar.bz2
```

2. 解压并使用
 完成下载后，执行解压指令：

 ```shell
tar xvf sublime_text_3_build_3143_x64.tar.bz2 
```
 程序被解压到 sublime_text_3 文件夹中。进入后执行 `./sublime_text` 即可使用。




### typora
程序员喜欢用什么写文档？当然是 markdown 啦！
强行安利一波 typora，虽然不开源，但是非常好用/免费/功能强大/颜值高/效率高……

![](http://qiniu1.letow.top/typora-sample-2017-11-04-20.09.jpg)

这是 [typora 官网](https://typora.io/)

```shell
# optional, but recommended
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
# add Typora's repository
sudo add-apt-repository 'deb http://typora.io linux/'
sudo apt-get update
# install typora
sudo apt-get install typora
```

`update`失败了？请往下看——



### xx-net（或其他 sock 5 工具） 

说实话，安装 typora 和 sublime 的时候都遭遇到了很大的挫折，不得已向 [xx-net](https://github.com/XX-net/XX-Net) 求助。
因为已经轻车熟路了，过程在我这里简化了很多，建议初学者画点精力来研究一下 xx-net 的用法，真的很实用。

linux 上很多关键功能需要在终端上完成，即使网页浏览器可以正常访问外网，但是如果终端无法使用也很可惜。

[Configure proxy for APT?](https://askubuntu.com/questions/257290/configure-proxy-for-apt)

#### 为 apt 工具配置使用代理

1. 如果没有 /etc/apt/apt.conf 文件，则新建

 ```shell
sudo vim /etc/apt/apt.conf
```

2. 写入 xx-net 端口，进行流量代理

 ```shell
Acquire::http::Proxy "http://127.0.0.1:8087";
```

 当然，如果你**使用蓝灯、ss等其他 sock 5 代理工具**，这里就应该改成它们**各自对应的端口**。

 现在再次尝试 `sudo apt-get update` 命令，发现出现了大量报错：

 ```
W: 仓库 “https://typora.io linux/ Release” 没有 Release 文件。
N: 无法认证来自该源的数据，所以使用它会带来潜在风险。
N: 参见 apt-secure(8) 手册以了解仓库创建和用户配置方面的细节。
W: 仓库 “https://typora.io ./linux/ Release” 没有 Release 文件。
N: 无法认证来自该源的数据，所以使用它会带来潜在风险。
N: 参见 apt-secure(8) 手册以了解仓库创建和用户配置方面的细节。
E: 无法下载 https://typora.io/linux/Packages  server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none
E: 无法下载 https://typora.io/./linux/Packages  server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none
```

 查找资料得知是由于 apt 工具的安全设置引起的：

 [Getting “server certificate verification failed” during apt-get update](https://askubuntu.com/questions/277793/getting-server-certificate-verification-failed-during-apt-get-update)

3. 添加例外站点

 ```shell
Acquire::https::typora.io::Verify-Peer "false";
```

 在 /etc/apt/apt.conf 中补充上面这句话，将 typora 的站点的 ssl 验证忽略。

4. 重新尝试，发现可以正常安装了



### indicator-sysmonitor

这是一款可以显示在状态栏的系统监控程序，精致实用，适用于喜欢掌控自己设备状况的人。

![](http://qiniu1.letow.top/indicator-2017-11-04-20.18.jpg)

```shell
sudo add-apt-repository ppa:fossfreedom/indicator-sysmonitor
sudo apt-get update
sudo apt install indicator-sysmonitor
```

然后在系统搜索中搜索 "ind" 就可以找到并启动，注意设置-general 中可以设置位随系统启动。



### unity-tweak-tool 与 Flatbulous

unity tweak tool 是 Ubuntu 上一款很流行的美化软件，用于 unity 桌面环境。

```shell
sudo apt-get install unity-tweak-tool
```

flatbulous 是一款扁平化主题，主要作用是美化菜单和应用图标等。

```shell
sudo add-apt-repository ppa:noobslab/themes
sudo apt-get update
sudo apt-get install flatabulous-theme
```

配套图标下载：

```shell
sudo add-apt-repository ppa:noobslab/icons
sudo apt-get update
sudo apt-get install ultra-flat-icons
```

在 unity tweak tool 更换主题和图标即可。



### GIMP 图片编辑器

这是在 Ubuntu软件 中下载的，用于编辑一些图片。事实证明，linux 上的这类多媒体编辑软件可能确实天生比 Windows 或 mac 平台有劣势。这款软件虽然不是专业版的 GIMP，但是功能也不算少，可是易用性比起 Photoshop 确实差了不少。也或许是我刚刚接触的原因吧。

![](http://qiniu1.letow.top/gimp-2017-11-04-20.51.jpg)



就先到这里吧，好饿啊。
