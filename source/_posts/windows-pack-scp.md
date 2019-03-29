---
title: Windows 封装 scp 命令
date: 2019-03-30 03:02:30
tags: [脚本]
---

## 序言

### 最终效果

![](http://qiniu1.letow.top/snipaste-2019.03.30-02.03.jpg)

运行：

```powershell
$ get /path/to/file
```

就把服务器的文件取到桌面上了。

### 为什么要这么做 ~~（闲的）~~

目的：就是方便从服务器获取文件嘛

直接原因：scp 命令太麻烦了

深层原因：云服务器开启 ftp 服务器失败了（一万年了，我按照教程操作就没有成功过）

<!--more-->

## 正文

### 了解 scp 命令

详细的不再赘述，请看：[每天一个linux命令（60）：scp命令](https://www.cnblogs.com/peida/archive/2013/03/15/2960802.html) 。

```powershell
PS D:\桌面> scp
usage: scp [-346BCpqrv] [-c cipher] [-F ssh_config] [-i identity_file]
           [-l limit] [-o ssh_option] [-P port] [-S program] source ... target
```

scp 命令格式跟 ssh 大致相同，从服务器下载文件类似于：

```shell
$ scp root@39.xxx.xxx.105:/path/to/file D:\\path\to\put\
```

跳过验证的关键点：使用 `-i` 指定身份验证文件。不过注意该项要放在路径之前。

### 身份验证文件

之前的文章提到过使用 putty 自动登录，已经制作了一个身份验证文件：[一次性打开多个 putty 连接](http://blog.letow.top/2019/03/13/several-putty-at-once/) 。不过这篇文章也没重复给出制作方法而是直接放了链接就是了……

不过如果你尝试直接用 `-i` 指定该文件，八成会获得下面的报错：

> Permissions for 'C:\\Windows\\System\\test.ppk' are too open.
> It is required that your private key files are NOT accessible by others.
> This private key will be ignored.

仍然需要输入密码啊喂！

Google 得知~~你太 open 了~~，文件权限问题，拥有这个文件访问权限的用户太多。把这个密钥文件属性改一下，右键菜单 ——

#### 更改所有者

`属性` - `安全` - `高级`

然后一个模态窗，title 是 `xxx.ppk的高级设置`。在所有者行点击“更改”，又是一个模态框 `更改用户或组`。在这里你要把所有者设置为自己，也就是登录 Windows 的账户：

`高级` - `立即查找`

找到带邮箱的你的名字，确定后下方的列表没有变化，但是仔细看“所有者”已经变成你的账号了。

![](http://qiniu1.letow.top/snipaste-2019.03.30-02.29.jpg)

更改完所有者，下一步是删除原有的所有用户权限。

#### 添加用户权限

在这之前，你要先把自己的用户添加至下方权限条目里，不然无法删除，会得到这样的提示：

> 因为 所有受限制的应用程序包 从其父系继承权限，你无法删除此对象。要删除 所有受限制的应用程序包，你必须阻止对象继承权限。关闭继承权限的选项，然后重试删除 所有受限制的应用程序包。

点击下方的 `添加` - `选择主体`，添加过程与之前一样。主体选择后，将权限勾选为“完全控制”，`确定`。

#### 删除其他用户

接下来，可以尝试选中某一个其他用户点击 `禁用继承` 按钮，在弹窗中选择 `从此对象中删除所有已继承的权限。`，直接全部删除，世界清静了。

如果选中其他用户后 `禁用继承` 按钮是禁用状态，先点 `🛡️ 更改权限` 按钮，窗口会刷新状态。

#### 再次测试

更改完了用户权限，现在行了吧？

> Load key "C:\\Windows\\System\\test.ppk": invalid format

openssh 表示没有见过这么奇葩的密钥格式。

#### 导出 openssh 专用格式

Google 之，发现需要导出 openssh 专用格式的密钥文件。

打开 puttygen（就是一开始生成密钥的软件），`load` 密钥文件 - 菜单栏 `Conversions` - `Export OpenSSH Key`。文件后缀名没有要求。

当然，你需要再次更改导出文件的权限（没错，之前改的没有用）。

不出意外，这次已经可以了。

### 封装 scp 命令

首先，bat 批处理文件用 `%1` 这种形式可以拿到命令行的第一个参数。事实上，`%0` 取到的是脚本路径。（关于这点 cmd 与 powershell 运行略有出入，本例中不影响使用）

保存文件 `a.bat` 测试：

```powershell
echo %0
echo %1
```

cmd 运行一下：

```
D:\桌面>a.bat test   # 以下是输出

D:\桌面>echo a.bat
a.bat

D:\桌面>echo test
test
```

powershell 运行一下：

```powershell
PS D:\桌面> .\a.bat test   # 以下是输出

D:\桌面>echo "D:\桌面\a.bat"
"D:\桌面\a.bat"

D:\桌面>echo test
test
```

所以对 scp 命令做以下封装：

```powershell
scp -i C:\\path\to\openssh-key root@39.105.105.113:%1 D:\桌面\
```

重命名为 `get.bat`，放到 `C:\\Windows\System23\` 里就完事了。调用方式就是 `get 文件位置`。

至于多个服务器、放置位置更改，请自行实现。

## 尾声

### 你说 Windows 没有 scp 命令？

请更新 Windows 10 最新版本；或自行下载安装 windows 版的 openssh，并添加至环境变量。

![](http://qiniu1.letow.top/5a6a01367cb67.JPG)

### 展望

有朝一日我王某人农奴翻身也用上 Mac，再也不受 Windows 的鸟气了。

![](http://qiniu1.letow.top/zhenxiang.jpg)