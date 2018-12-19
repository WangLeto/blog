---
title: ubunut 下使用 vs code 调试 c++ 程序，查看数组内容 
date: 2018-08-11 12:10:39
tags: [Linux, vs code]
---
稍微试了下用 vs code 编写 c++ 程序，感觉还不错。这里给出一些调试相关的经验。

![](http://qiniu1.letow.top/2018-08-11%2010-46-52%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.jpg)

<!--more-->

## 路径配置

首先要注意的是，Linux 下 vs code 如果使用含中文的项目路径，会出现文件不存在之类的错误，因此必须使用全英文路径。

## 配置文件

点击工具栏的第四个按钮，即调试按钮，然后点击绿色的调试按钮，会自动添加一份配置文件 `launch.json`。

![](http://qiniu1.letow.top/2018-08-11%2010-53-18%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.jpg)

修改配置文件的 `program` 项：

```json
"program": "${workspaceFolder}/a.out"
```

## 编译源文件

首先在终端运行命令（假设你的源文件是 `test.cpp`）：

```bash
g++ ./test.cpp -g
```

g++ 编译默认输出的文件名为 `a.out`，如果想要指定名称，可以使用 `-o b.out` 参数指定输出文件名为 `b.out`。但是注意，上面的 `launch.json` 文件也要对应的改为 `"program": "${workspaceFolder}/b.out"`。

`-g` 参数允许使用 gdb 调试，否则 vs code 的调试无法正常使用。

此时，已经可以在源文件打断点，然后点击调试按钮进行调试了。

## 设定任务文件

如果感觉上面每次进行编译太过麻烦，我们可以将编译的工作交给 vs code 来做。

按下快捷键 `Ctrl + Shift + B`，这是 vs code 中的运行生成任务的快捷键，由于没有配置文件，所以会提醒你新建配置文件。

![](http://qiniu1.letow.top/2018-08-11%2011-11-52%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.jpg)

选择任务的模板为 `Others`。

![](http://qiniu1.letow.top/2018-08-11%2011-12-03%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.jpg)

生成之后，返回源文件再次按下快捷键，会提示没有找到要运行的生成文件，点击确认即可。这样就将这个项目的全部生成任务设置为了那个配置文件。

```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "echo",
            "type": "shell",
            "command": "echo Hello",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```

此时的 `task.json` 文件是这样的。

将 command 选项改为：

```json
"command": "g++ ./你的源文件.cpp -o 所需的生成文件.out -g"
```

其实就是将上面编译的命令输入，每次运行任务时自动调用。

此时按下快捷键 `Ctrl + Shift + B`，已经可以直接输出了。

## 查看 c++ 数组

在调试过程中，有时候需要查看数组内容，但是 vs code 并没有这个功能。

可以通过添加监视对象来解决，右键点击监视窗格，添加表达式：

```c++
*(int(*)[10])arr_name
```

其中的变量类型可以是自定义类型。



![](http://qiniu1.letow.top/2018-08-11%2011-34-45%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.jpg)
