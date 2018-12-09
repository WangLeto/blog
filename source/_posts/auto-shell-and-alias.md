---
title: 使用 sh 脚本在 ubuntu 上执行自动命令<br>使用 alias 进行命令简化
date: 2017-12-21 01:19:42
tags: [linux]
---
脚本高效方便，无论是使用 linux 上的简单的 shell 脚本还是 python 写的稍大的程序，都可以自动完成或简化很多繁琐的工作。这里介绍一些简单的 Ubuntu 脚本。

## ssh 登录阿里云主机

参考教程已经找不到了，这里介绍广泛使用的 expect 包。

<!--more-->

常规的登录流程：

```shell
ssh user@10.10.10.10
user@10.10.10.10's password: **
[user@xxxx ~]#
```

1. 首先安装所需的包 expect：

   ```shell
   sudo apt update
   sudo apt install expect
   ```


2. 然后新建文件 `auto-ssh.sh`

3. 写入脚本

   ```shell
   #! /usr/bin/expect

   spawn ssh user@10.10.10.10
   expect "*password*"

   send "你的密码\r\n"

   interact
   ```

4. 保存退出，修改权限

   ```shell
   chmod +x ./auto-ssh.sh
   ```

5. 测试脚本

   ```shell
   ./auto-ssh.sh
   ```

   此时应该可以正常地登录了。 


### 关于 ssh 登录安全性

很显然，将密码写在脚本中不是很安全的做法，如果你很在意这一点，又想要简化登录流程，其实还有更好的方法：[设置 SSH 通过密钥登录](https://hyjk2000.github.io/2012/03/16/how-to-set-up-ssh-keys/)。

我在 Windows 上使用 Putty 客户端进行登录，就是采用了公私钥认证的方法，简单方便，这里不再细述。



## 使用 alias 设为常用命令

完成脚本后，如果每次使用都需要找到脚本文件再执行，效率仍然比较低下。我们再利用 linux 上的 alias 命令，将脚本的执行设置为简单的命令。

1. 关于 alias 命令

   可以参考这里：[alias命令](http://man.linuxde.net/alias)，简而言之就是为指令起个别名，也可以直接指向脚本。

   直接运行 `alias` 命令，你可以看到当前存在的别名设置列表。

2. 设置 alias 

   假设脚本的路径是 `~/commands/auto-ssh.sh`（ `~` 即用户目录，通常代表的是 `/home/用户名`，以 root 用户登录时在根目录的 `/root`下），在终端输入命令：

   ```shell
   alias a-sh='~/commands/auto-ssh.sh'
   ```

   这样，你就在任何目录下输入命令 `a-sh` 来执行这个脚本了。

3. 写入 **.bashrc** 文件

   如果仅仅是在终端执行 alias 命令，下次开机时会发现设置的命令已经失效了，因此我们需要将这个命令写入 `~/.bashrc` 文件。

   可以在 `~/.bashrc` 文件中查找 `alias`，应该可以找到已经存在的别名设置，将你自定义的别名命令添加到下面即可。




## 在脚本中输入 sudo 密码

有时在执行脚本的时候，会出现因为权限不足需要输入 sudo 密码的情况，可以通过简单的命令来自动输入。当然，<u>如果你不希望自己的密码写入脚本，这个技巧也是无用的</u>。

转自 [ubuntu运行sh脚本sudo自动输入密码](https://segmentfault.com/a/1190000004950264)，有删改。

1. 可以采用文本块输入重定向：

  ```shell
  #! /bin/bash
  sudo -S apt update << EOF 
  你的密码
  EOF
  ```

2. 也可以使用管道：

   ```shell
   echo 你的密码 | sudo -S apt update
   ```


### -S 参数

```shell
man sudo
```

描述：

>     -S, --stdin
>     Write the prompt to the standard error and read the password from the standard input instead of using the terminal device. The password must be followed by a newline character.

参数将错误输出和密码读入定位到标准输入输出，不再与终端设备交互，密码需要在新的一行中传入。
