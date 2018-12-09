---
title: pip 安装包时报 utf-8 相关编码错误
date: 2017-12-05 14:34:53
tags: [python,pip]
---
在 Windows 上使用 python 的包管理工具 pip 的时候，经常出现下面这种让人虎躯一震的情形：

![](http://qiniu1.letow.top/pip-utf8-error-2017.12.05-14.36.jpg)

在这里找到了可以使用的解决方案：[Windows上Python3通过pip安装包出现UnicodeDecodeError](https://segmentfault.com/q/1010000008071661)

<!--more-->

方法其实很简单：

打开安装 python 的路径（如 `c:\program files\python36`），找到文件 `lib\site-packages\pip\compat\__init__.py`，修改大约 75 行位置的

```python
return s.decode('utf-8')
```

为

```python
return s.decode('cp936')
```



## 2018.1.18 更新

要不然说年轻人就是 too young，今天重装 python 27 再次遇到了这个问题，看着自己写的博客不禁陷入了沉思……

python2 下出现 pip 错误的正确解决方案：[【python】pip安装报错UnicodeDecodeError: 'ascii' codec can't decode byte 0xce in position 7: ordinal not i](http://blog.csdn.net/u013948858/article/details/77800663) 。

没想到 **Windows 使用中文用户名**再次坑了自己一次。

### 解决方案 1

python 安装目录的 \Lib\site-packages 目录新建文件 sitecustomize.py

```python
import os
sys.setdefaultencoding('gb2312')
```

就可以为 python 设置系统默认编码。

### 解决方案 2

修改安装目录下的 \Lib\mimetypes.py 文件

在 import 下添加

```python
if sys.getdefaultencoding() != 'gbk'
    reload(sys)
    sys.setdefaultencoding('gbk')
```


