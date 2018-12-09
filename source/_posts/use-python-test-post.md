---
title: 使用 python 脚本快速测试 post 请求

data: 2018-05-18 01:29:29

tags: python

---

因为 chrome 无法模拟发送 post 请求，因此之前找了一个叫 postman 的工具进行服务端的 post 请求测试，结果出现各种迷之 bug。后来才发现 python 做这种事情简直不要太爽：

```python
import requests

url = 'http://127.0.0.1:8000'
d = {'op': 'add', 'id': '1', 'text': 'test', 'password': 'xxxxxx'}
r = requests.post(url, data=d)
print(r.text)
print(r.status_code)
```


