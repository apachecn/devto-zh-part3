# 带有谷歌云功能的 HTML 模板

> 原文：<https://dev.to/googlecloud/html-templates-with-google-cloud-functions-29bc>

Google Cloud 函数可以响应多种类型的事件，其中最有用的事件是 HTTP 请求。这允许您向云函数提供的 URL 发出一个`GET`、`POST`或其他 HTTP 请求，并让它运行以响应给定的请求。

当您通过 HTTP 激活您的云功能时，您也可以选择返回一个响应，就像 HTTP 服务器一样。在最简单的例子中，这通常只是一个字符串:

```
def hello(request):
    return "Hello, World!" 
```

这将创建一个如下所示的 HTTP 响应:

```
HTTP/1.0 200 OK
Content-Length: 13
Content-Type: text/html; charset=utf-8

Hello, World! 
```

您会注意到这里的`Content-Type`是`text/html`，但是响应根本不是 HTML，它只是明文。如果我们想添加一些格式呢？

# 添加一些格式

假设我们想让我们的部分回复**加粗**。我们可以通过添加一些 HTML 标签来做到这一点:

```
def hello(request):
    return "Hello, <b>World!</b>" 
```

这将给我们以下响应:

```
HTTP/1.0 200 OK
Content-Length: 20
Content-Type: text/html; charset=utf-8

Hello, <b>World!</b> 
```

这可能会在我们的浏览器中正确呈现，但这只是因为现代浏览器在处理无效 HTML 方面有着惊人的倾向。为了使这实际上成为一个有效的 HTML 响应，我们需要将函数改为:

```
def hello(request):
    return """
<!DOCTYPE html>
<head>
  Saying Hello
</head>
<body>
  Hello, <b>World!</b>
</body>
""" 
```

这开始变得有点长了，所以也许我们可以把它变成一个单独的变量:

```
INDEX_TEMPLATE = """
<!DOCTYPE html>
<head>
  Saying Hello
</head>
<body>
  Hello, <b>World!</b>
</body>
"""

def hello(request):
    return INDEX_TEMPLATE 
```

# 传递变量和控制流程

如果我们想将变量传递到模板中，我们可以用`.format` :
来完成

```
INDEX_TEMPLATE = """
<!DOCTYPE html>
<head>
  Saying Hello
</head>
<body>
  Hello, <b>{place}!</b>
</body>
"""

def hello(request):
    return INDEX_TEMPLATE.format(place="World") 
```

如果我们想在不同的线路上和多个地方打招呼，会怎么样？

```
def hello(request):
    return INDEX_TEMPLATE.format(places=["World", "dev.to"]) 
```

在这一点上，我们的“自制”模板将开始崩溃，为了让它继续工作，我们需要开始变得有创造性。

# 带云功能的内嵌模板

相反，让我们使用一些现有的工具。既然头罩下的云函数是使用 Flask，这就意味着 [`render_template_string`](http://flask.pocoo.org/docs/1.0/api/#flask.render_template) 函数可供我们使用。这让我们能够以与我们的自定义解决方案相似的方式呈现一个 [Jinja2](http://jinja.pocoo.org/docs/) 模板，而且还让我们能够执行循环、if 语句等:

```
from flask import render_template_string

INDEX_TEMPLATE = """
<!DOCTYPE html>
<head>
  Saying Hello
</head>
<body>
  {% for place in places %}
  Hello, <b>{{ place }}!</b><br>
  {% endfor %}
</body>
"""

def hello(request):
    return render_template_string(
        INDEX_TEMPLATE, places=["World", "dev.to"]
    ) 
```

这将产生如下响应:

```
HTTP/1.0 200 OK
Content-Length: 140
Content-Type: text/html; charset=utf-8

<!DOCTYPE html>
<head>
  Saying Hello
</head>
<body>
  Hello, <b>World!</b><br>
  Hello, <b>dev.to!</b><br>
</body> 
```

# 模板目录

最后，也许你的模板相当长，你不希望它和你的函数存在于同一个文件中，你可以把它放在一个名为`templates`的目录中，用 [`render_template`](http://flask.pocoo.org/docs/1.0/api/#flask.render_template) 函数来代替。

你的源文件应该是这样的:

```
├── main.py
├── requirements.txt
└── templates
    └── hello.html 
```

其中`hello.html`与我们之前的示例相同:

```
<!DOCTYPE html>
<head>
  Saying Hello
</head>
<body>
  {% for place in places %}
  Hello, <b>{{ place }}!</b><br>
  {% endfor %}
</body> 
```

以及这样配置的功能:

```
from flask import render_template

def hello(request):
    return render_template('hello.html', places=["World", "dev.to"]) 
```

就是这样！从这里开始，您可以使用 Jinja2 的所有特性，就像在其他模板中一样。