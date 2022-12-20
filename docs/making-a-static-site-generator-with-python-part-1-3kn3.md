# ⚡️用 Python 制作静态站点生成器——第 1 部分

> 原文：<https://dev.to/nqcm/making-a-static-site-generator-with-python-part-1-3kn3>

[![static Site Generators in Python](img/fa6d070164d9167a715f5af10d176562.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OKRs0Eu5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k7sxavnuzyduny8g1src.jpg)

静态站点生成器是传统内容管理系统(如 Wordpress)的一种安全快速的替代品。

一旦你掌握了静态站点生成器或 SSG，使用起来非常简单。但是最初可能会有一个学习曲线。学习的一种方式(我更喜欢的方式)是观察引擎盖下的东西，看看它们是如何工作的，并尝试重现它们。考虑到这个最终目标，我们将使用 Python 创建我们自己的静态站点生成器。另外，做起来很有趣。

如果你是静态站点生成器的新手，你可以先阅读一下[什么是静态站点生成器](https://davidwalsh.name/introduction-static-site-generators)、[何时使用一个](https://learn.cloudcannon.com/jekyll/why-use-a-static-site-generator/)，以及[何时不使用一个](https://www.sitepoint.com/7-reasons-not-use-static-site-generator/)。还可以看看用不同编程语言编写的静态站点生成器列表。

所以事不宜迟，我们开始吧。

## 了解静态站点生成器如何工作

统计站点生成器背后的概念非常简单。你在 markdown 中写文章，静态站点生成器中的脚本使用预先创建的模板将你的 markdown 转换成 HTML。这些静态 HTML 文件可以上传到任何 web 服务器，并提供给读者。

假设你正在制作一个食谱博客。你用 markdown 这样写你的食谱:

```
title: Turkish Pide
date: 17-04-19

**Ingredients for the dough:**
500 gm bread flour
1 tblspoon yeast
1 tblspoon honey
1 tsp salt
2 tblsp olive oil
250 ml or as req water.

**Ingredients for the filling:**
1 cup grated Mozerella
½ cup crumbled feta 

Combine the ingredients of the dough and knead until smooth and elastic. Let rest 45 minutes. Divide in two. Roll each in a rectangle. Spread the cheese filling over top. And roll the sides in to form a boat shape. Let rise for 10-15 minutes. Brush the sides with olive oil and bake at 230C for 10-15 minutes or until nicely browned. 
```

Enter fullscreen mode Exit fullscreen mode

你有一个这样的模板:

```
<html>
    <head>
        {{ title }}
    </head>

    <body>
        <p>
            Published at: {{ date }}
            {{ content }}
        </p>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

静态站点生成器将解析 markdown 文本，并将每个组件插入其所属的位置，因此您将得到类似这样的结果:

```
<html>
    <head>
        Turkish Pide
    </head>

    <body>
        <p>
            Published at: 17-04-19
            <strong>Ingredients for the dough:</strong><br>
            <ul>
                <li>500 gm bread flour</li>
                <li>1 tblspoon yeast</li>
                <li>1 tblspoon honey</li>
                <li>1 tsp salt</li>
                <li>2 tblsp olive oil</li>
                <li>250 ml or as req water.</li>
            </ul>

            <strong>Ingredients for the filling:</strong>
            <ul>
                <li>1 cup grated Mozerella</li>
                <li>½ cup crumbled feta</li>
            </ul>

            Combine the ingredients of the dough and knead until smooth and 
elastic. Let rest 45 minutes. Divide in two. Roll each in a rectangle. Spread the
cheese filling over top. And roll the sides in to form a boat shape. Let rise for
10-15 minutes. Brush the sides with olive oil and bake at 230C for 10-15 minutes
or until nicely browned.
        </p>        
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们需要找到一种方法来解析从 markdown 到 HTML 的文本，并将 HTML 插入到预先创建的模板中。

## 将 Markdown 转换为 HTML

创建一个新的文件夹来存放你的代码:

```
mkdir recipe-ssg 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，SSG 代表静态站点生成器。尽管它对我网站的 SEO 有好处，但我已经厌倦了一遍又一遍地输入静态站点生成器，所以从现在开始我将把静态站点生成器称为 SSG。唷！

在这个文件夹中，创建内容文件夹，我们将在这个文件夹中以 markdown 文件的形式写我们的博客文章:

```
cd recipe-ssg & mkdir content 
```

Enter fullscreen mode Exit fullscreen mode

在内容文件夹中创建一个降价文件。你可以复制[这个文件](https://raw.githubusercontent.com/nqcm/static-site-generator/master/content/turkish-pide.md)的内容，并将其命名为`turkish-pide.md`。

您会注意到该文件分为两部分。两个破折号分隔符之间有一些元数据。而实际内容在分隔符下面。

接下来，我们需要一种方法来解析这个 markdown，将其转换为 HTML 并存储这些数据，以便 Python 可以使用这些数据。但是我们不需要重新发明轮子。令人敬畏的 Python 社区已经为我们编写了一个 [Markdown 解析器](https://github.com/trentm/python-markdown2)。我们将安装它并尝试一下，看看它是如何工作的。

在您的[虚拟环境](https://docs.python-guide.org/dev/virtualenvs/)中，使用 pipenv 或 pip 安装`markdown2`。

```
pipenv install markdown2 
```

Enter fullscreen mode Exit fullscreen mode

在交互式 Python shell 中尝试:

```
from markdown2 import markdown
markdown("**This is a very important message**")

# outputs '<p><strong>This is a very important message</strong></p>\n' 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们可以创建一个简单的脚本来将 markdown 格式文件转换成 HTML 文件。

在项目的根目录下(不在内容中)创建一个`main.py`文件，并键入以下内容:

```
from markdown2 import markdown

with open('content/turkish-pide.md', 'r') as file:
    parsed_md = markdown(file.read(), extras=['metadata'])

print('Metadata: ', parsed_md.metadata)
print('Content: ', parsed_md) 
```

Enter fullscreen mode Exit fullscreen mode

运行`main.py`，你会看到`turkish-pide.md`文件的内容用 HTML 标签打印在控制台上。还要注意，元数据被转换成了 Python 字典。我们现在可以使用这些变量来创建使用模板的页面。

但是首先我们需要创建一个模板。

## 创建和使用模板

模板是普通的 HTML 文件，带有变量数据的占位符。我们将使用 Python 的 [Jinja2](http://jinja.pocoo.org/docs/2.10) 模板语言，它将简单地从 HTML 模板中的变量插入数据。如果你是模板语言或 Jinja2 的新手，你可以先在这里阅读更多关于它的内容[。](https://realpython.com/primer-on-jinja-templating/)

使用 pipenv 或 pip 在您的虚拟环境中安装【T0:

```
pipenv install jinja2 
```

Enter fullscreen mode Exit fullscreen mode

要尝试 Jinja2，在项目根目录下创建一个文件夹，并将其命名为`templates`。现在在`templates`中创建一个`test.html`，并将下面的代码放入其中:

```
<!DOCTYPE html>

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    {{ post.title }}
</head>
<body>

    <h1>{{ post.title }}</h1>
    <small>{{ post.date }}</small>
    <p>
        {{ post.content }}
    </p>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们有了一个模板，我们只需要从解析后的 markdown 中提取变量，并将它们交给 Jinja2。

在您的`main.py`中添加以下代码:

```
from jinja2 import Environment, PackageLoader

env = Environment(loader=PackageLoader('main', 'templates'))
test_template = env.get_template('test.html')

data = {
    'content': parsed_md,
    'title': parsed_md.metadata['title'],
    'date': parsed_md.metadata['date']
}

print(test_template.render(post=data)) 
```

Enter fullscreen mode Exit fullscreen mode

导入 Jinja2 后，我们创建一个环境来显示模板文件夹所在的位置。确保`PackageLoader`的第一个参数是 python 文件的名称，在我的例子中是`main`。接下来我们得到我们需要的模板，从我们解析的文件中提取数据后，我们把它交给 Jinja2，同时调用`render()`函数。

您的最终代码现在应该是这样的:

```
from markdown2 import markdown
from jinja2 import Environment, PackageLoader

with open('content/turkish-pide.md', 'r') as file:
    parsed_md = markdown(file.read(), extras=['metadata'])

    env = Environment(loader=PackageLoader('main', 'templates'))
    test_template = env.get_template('test.html')

    data = {
    'content': parsed_md,
    'title': parsed_md.metadata['title'],
    'date': parsed_md.metadata['date']
    }

    print(test_template.render(post=data)) 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在运行你的`main.py`,你应该从你的`test.html`文件中得到所有的代码，你的`turkish-pide.md`文件中的内容被插入到合适的地方。

现在我们知道了如何使用 Jinja2 渲染 HTML 页面，在[下一部分](https://blog.naveeraashraf.com/posts/make-static-site-generator-with-python-2/)我们将把所有的部分放在一起，制作我们的 SSG。

*特别感谢[贾汉吉尔·拉赫莫诺夫](https://rahmonov.me)，我从他那里学到了这些概念，[他的文章](https://rahmonov.me/posts/static-site-generator/)是本教程的基础。*