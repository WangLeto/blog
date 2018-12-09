---
title: python 中文名用户文件夹 ntpath.py 错误
date: 2018-02-04 17:31:04
tags: [python, encode]
---

被 python 的编码机制坑了不是一次两次了，尤其是在 python2 中对 unicode 的处理更让人头大。

最近在程序中遇到的一个问题是，python 自带的文件 ntpath.py 在处理用户文件夹路径的时候，会因为用户文件夹名称含有中文报错：

```
File "C:\OpenSource\python27\lib\ntpath.py", line 92, in join
  result_path = result_path + p_path
```

## 解决方案

在 ntpath.py 文件头部的引入下方添加：

```python
reload(sys)
sys.setdefaultencodeing('utf-8')
```

将报错的一行改为：

```python
try:
    result_path = result_path.decode('gb2312')
except:
    pass
result_path = result_path + p_path
```

