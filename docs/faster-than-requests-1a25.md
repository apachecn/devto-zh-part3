# 比请求更快

> 原文：<https://dev.to/juancarlospaco/faster-than-requests-1a25>

[![Faster Than Requests](img/279abeee864902da57f9582dfe244336.png "Faster Than Requests")](https://res.cloudinary.com/practicaldev/image/fetch/s--fT0wKqn4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6fzgmcqiv85vgjdiqwyl.jpg)

| 图书馆 | 速度 | 文件 | 通信线路（LinesofCommunication） | 属国 | 开发商 |
| --- | --- | --- | --- | --- | --- |
| 皮格莱特 | `152.39` | one | Three hundred and thirty-eight | Wget | >17 |
| 要求 | `15.58` | >20 | Two thousand five hundred and fifty-eight | > 7(取决于 Urllib3) | >527 |
| 请求(预缓存) | `5.50` | >20 | Two thousand five hundred and fifty-eight | > 7(取决于 Urllib3) | >527 |
| Urllib | `4.00` | ？？？ | One thousand two hundred | 0(标准库) | ？？？ |
| Urllib3 | `3.55` | >40 | Five thousand two hundred and forty-two | 0 (No SSL), >5 (SSL) | >188 |
| PyCurl | `0.75` | >15 | Five thousand nine hundred and thirty-two | 库勒，库勒 | >50 |
| PyCurl(无 SSL) | `0.68` | >15 | Five thousand nine hundred and thirty-two | 库勒，库勒 | >50 |
| 比请求更快 | `0.45` | one | Seventy-five | Zero | one |

`faster_than_requests`新版本发布！，
上传到 PyPI，PIP installable，
我们添加了新的文档、示例、Dockerfile、测试、代理、FAQ、CoC、调试、错误修复、JSON 助手、环境变量感知、+200 提交，以及更多！。

`faster_than_requests`它是 Python 3 上的请求和 HTTPS 客户端的替代品，
比请求快 15 倍，比 PyCurl 快 2 倍，
0 个依赖项，1 个文件，~100 行代码。

API 很简单，不是有一对你应该提供很多参数的函数来使它工作，我们有很少参数的小函数来做一件事，而且做得越快越好。

具有人们要求的额外功能，面向
数据科学、大数据、开放数据、Web 报废，使用 HTTP REST JSON APIs。

*   我们有一个码头工人，不用安装任何东西就可以试驾它。

新文档包含所有带有详细参数的函数和带有类型的返回。

# 请求依赖于 Urllib3

请求现在依赖于 Urllib3，使得代码行总数超过 8.000 行。

8000 行代码下载一个猫迷因。

`faster_than_requests`用约 100 LoC 替换 8.000 LoC:)

我们也没有依赖，也没有*筹款*。

# 你好世界

```
import faster_than_requests as req

print(req.gets("http://example.org/get"))                     # GET print(req.posts("http://example.org/post", "Some Data Here")) # POST 
```

Enter fullscreen mode Exit fullscreen mode

# 趁热吃吧

*   [https://github . com/juancarlospaco/fast-than-requests # fast-than-requests](https://github.com/juancarlospaco/faster-than-requests#faster-than-requests)
*   [https://pypi.org/project/faster_than_requests](https://pypi.org/project/faster_than_requests)
*   `pip install faster_than_requests`

*请在 GitHub 上以快于请求的速度启动！很棒的东西来了...*