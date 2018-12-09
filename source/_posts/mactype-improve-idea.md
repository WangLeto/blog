---
title: 使用 mactype 优化 idea 全家桶字体效果
date: 2018-07-01 00:00:27
tags: [其他]
---

> mactype 是一款老牌的 Windows 字体渲染优化软件，在 win7 以及更早时代曾风靡一时，但是在某些 Windows 死忠眼中，这俨然是 pc 对阵 mac 的一大污点。随着高分屏时代的到来以及微软在 win10 上做出的字体优化努力，这款散发着旧时代气息的优化软件慢慢淡出新一代的视线。
>
> ——我自己编的

说实话，虽然是 JetBrains 的铁粉，但是我对他们糟糕的字体渲染表现一向是看不惯的。无数次看着 vs code 圆润光滑、散发着优雅气息的字体渲染效果，再看 idea 全家桶歪歪扭扭的字体表现（这还不算部分人从来都是使用默认字体亮瞎眼），简直是悲从中来，不可断绝。

<!--more-->

## 对比

同样使用 Fira Code 字体，下面左图是 webstorm 的显示效果，右图是 vs code 的效果：

![](http://qiniu1.letow.top/Untitled%201-2018-6-30-22-37.jpg)

对比还是比较明显的。

## 安装 mactype

登录 [mactype 官网](http://www.mactype.net/) 下载安装即可。

![](http://qiniu1.letow.top/snipaste%202018.06.30-23.39.jpg)

在配置向导中，我选择了不使用自动加载一项。因为我只需要将对应的 IDE 字体渲染借助 mactype 完成，而不希望全局所有程序使用。

上面两种加载方式介绍的也很明白，如果需要的话也可以选择。

## 使用 mactype 加载对应程序

虽然截图中写的是将需要的程序拖动到 MacType.exe 上手动加载，但是实际上这个文件并不存在。

经排查发现，在 mactype 的安装目录存在两个分别名为 MacLoader.exeMacLoader64.exe 的可疑文件。将 webstorm 的主程序拖动到这个 MacLoader64.exe 上代开，下面是显示效果：

![](http://qiniu1.letow.top/snipaste%202018.06.30-23.46.jpg)

显然字体显示结果大为不同。

## 新的打开方式

如果尝试直接双击打开上述的两个可执行文件，会报出如下错误：

```
---------------------------
MacType
---------------------------
MacType.exe <file> argument ...

Please drag and drop executable files or shortcut files.
---------------------------
确定   
---------------------------
```

（难道我没说过 Windows 大部分对话框直接按 Ctrl + C 可以复制内容吗）

很明显，我们需要一个 `file` 参数。直接使用命令行或者 powershell 运行：

```powershell
PS C:\Program Files\MacType> .\MacLoader64.exe "C:\Program Files\JetBrains\WebStorm 2018.1.5\bin\webstorm64.exe"
```

效果相同。

因此如果感觉拖拽程序麻烦，可以写一个批处理文件，前后分别写上 mactype 的绝对路径与目标程序的绝对路径：

```powershell
"C:\Program Files\MacType\MacLoader64.exe" "C:\Program Files (x86)\EditPlus 3\editplus.exe"
```

也就是两个绝对路径字符串即可。将其重命名为 `.bat` 扩展名文件，需要时双击打开即可，也可以固定到开始屏幕哦。
