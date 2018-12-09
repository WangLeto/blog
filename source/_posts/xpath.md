---
title: xpath 速查
date: 2018-02-02 01:13:09
tags: [xpath, 爬虫]
---

xpath很方便，这里简单做个速查记录。（在 chrome 审查元素时可以直接 copy xpath）

# 语法

## JavaScript

```javascript
document.evaluate('//body', document.documentElement, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null)
```

## xpath 语法

| nodename | 选取此节点的所有子节点。                  |
| -------- | ----------------------------- |
| /        | 从根节点选取。                       |
| //       | 从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。 |
| .        | 选取当前节点。                       |
| ..       | 选取当前节点的父节点。                   |
| @        | 选取属性。                         |

<!--more-->

## python 中的例句（selenium）：

```python
comics_list = driver.find_elements_by_xpath('//div[@id="chapter-list-0"]/ul/li')
```

# 快速制作脚本

## python 脚本框架

结合 selenium 和 chrome 审查元素，可以快速开发自动化网页操作脚本。

```python
import time
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

# 驱动中的 chrome 驱动
d = webdriver.Chrome(executable_path="绝对路径\chromedriver.exe")
d.get('http://首页')

# 等待网页响应，如果过快操作会失败
time.sleep(1)
d.find_element_by_xpath('//balabala').send_keys('account')
d.find_element_by_xpath('//bilibili').click()
```

借助 chrome 审查元素的 xpath，可以快速进行类似的脚本开发

## 处理中文输入

send_keys 方法不支持直接输入中文。不过我暂时没有用到，待更
