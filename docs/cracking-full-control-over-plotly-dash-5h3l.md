# 破解完全控制剧情. ly 破折号

> 原文：<https://dev.to/hackersandslackers/cracking-full-control-over-plotly-dash-5h3l>

[![Cracking Full Control Over Plot.ly Dash](img/4cf6f77954d65f58e99c7f9734460ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--87Adzp9z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res-3.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/Dash.jpg)

啊哈，**；在帖子标题中输入这个名字会引发一种骄傲和尴尬的情感混合。多年来，Plotly 一直是我参与开发的一些最有影响力的产品的核心:金融科技和人道主义客户的大杂烩，所有这些客户仍在世界各地自豪地挥舞着他们的图表和仪表盘。然而，我的头脑被一个简单的问题所困扰:到底是什么让我们花了这么长时间来写关于 Plotly 的第一篇文章？我们经营黑客和懒鬼已经整整一年了...在达到这一点之前，我在那段时间里认真地写了一篇关于 JQuery 的[帖子吗？](https://hackersandslackers.com/making-ajax-calls-with-jquery/)**

 **在过去一年左右的时间里，我们在蒙特利尔的朋友们发生了很大的变化。在我看来，排名第一的是他们核心产品的降价:从 300 美元(T0)降到零(T3)。我付了 300 美元。我们真的需要一个“捐赠”按钮。

紧随其后的无疑是**剧情简介**。Dash 勾起了许多年轻而又天真的 Python 爱好者的想法:*如果我们可以用 Python 只写***，就像* ***永远写*** *会怎么样？开始谷歌 Python-to-frontend 代码解释器(它们存在；我检查过)，Plotly 的 Dash 做了一件令人震惊的好工作，将生命注入到永远致力于 Python 的浪漫幻想中。**

 *但是我们在这里不是为了传递一个可回收的*‘什么是阴谋？’*剧情简介。我们甚至对强制性的*“如何开始使用这种已经被充分证明的技术”*帖子不感兴趣。普洛特利应该得到更好的。相反，我们从一开始就摇摆不定:我们将向你展示如何击败 Plotly，打破它，并使它屈服于你的意志。欢迎来到黑客 Plotly 的魔法版。伙计们，今天一定是圣诞节。

## 让我们从地狱制造一个神秘的情人

就像今年 Python 架构的几乎所有进步一样，Dash 有一个小秘密:它是在 Flask 的帮助下实现的。好吧，也许不止一点:Dash 实际上扩展了 Flask。听起来很有道理，甚至可能一开始就令人兴奋；就好像你曾经迷恋过的每一个人都决定最好是把他们的分歧放在一边，开始和你进行群聊，为了让你的性健康成为纯粹出于爱的平等的团队努力。正如你已经猜到的，生活并不是这样的。

从我们实例化应用程序的方式开始，Dash 劫持 Flask。任何看到过 **wsgi.py** 文件的代码猴子都可以在你说`app = dash.Dash( __name__ )`之前告诉你有事情发生了。查看推荐的启动样板文件:

```
from dash import Dash
import dash_core_components as dcc
import dash_html_components as html

external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

app = Dash( __name__ , external_stylesheets=external_stylesheets)

app.layout = html.Div(
        id='example-div-element'
        )

if __name__ == ' __main__':
    app.run_server(debug=True) 
```

如果您尝试使用这个样板文件并尝试添加核心 Flask 逻辑，比如用`Flask-Login`进行身份验证，用`Flask-Assets`生成资产，或者只是创建一个全局数据库，您会从哪里开始呢？Plot.ly 巧妙地建议为你的应用程序保留`app`名称空间——就像我们对 Flask 做的一样。然而，如果我们试图像修改 Flask 一样修改`app`对象，什么都不会起作用:Dash 已经声明了一个生态系统，并且在这个生态系统中没有任何地方邀请您添加现成的定制 Flask 应用程序逻辑。

Dash 很好地完成了它的初衷:构建基于仪表板的应用程序。问题是只能显示数据的应用程序作为最终产品并不完全有用。如果我们想要创建一个全功能的应用程序，而数据可视化只是该应用程序的一个功能，会怎么样？

## 创建功能全面的应用程序，其中数据可视化只是该应用程序的一个功能

社区中常见的“变通方法”是将 Flask 作为底层“服务器”传递给 Dash，如下所示:

```
from flask import Flask
from dash import Dash
import dash_core_components as dcc
import dash_html_components as html

server = Flask( __name__ )
app = dash.Dash( __name__ , server=server, url_base_pathname='/path')
app.layout = html.Div(id='example-div-element')

@server.route("/dash")
def MyDashApp():
    return app.index() 
```

毫无疑问:这种方法*糟透了。*当然，你已经恢复了到处创建路线的能力，但我们不要忘记:

*   你的应用程序将总是从 Dash 服务的页面开始:如果有的话，我们希望我们的开始页面是我们可以完全控制的，然后进入 Dash 组件。
*   在这种方法中，对全局可用的 Flask 插件的访问仍然不可用。注意我们从来没有设置应用程序上下文？
*   您用静态资产和样式来设计应用程序的能力完全不在您的掌控之中。
*   当我们启动非 Flask 的东西时，构建在 Flask 上的容器架构，如 Google App Engine，将不会很好地发挥作用。因此，遵守规则很有可能意味着失去部署能力。

如果我们想做这些事情，我们不能将我们的应用程序作为 Dash 的一个实例来启动，并试图绕过它。相反，我们必须创建一个 Flask 应用程序，并将 Dash 作为一个应用程序嵌入到我们的应用程序中。这使我们可以完全控制用户何时可以进入 Dash 界面，即使在该界面中，我们仍然可以根据需要管理数据库连接或用户会话。欢迎来到大联盟。

## 扭转局势:突进烧瓶内

首先，让我们拿回我们的文件。几乎所有托管的 Python 应用程序都希望如此，所以不要再说什么 **app.py** 了。

```
from application import create_app

app = create_app()

if __name__ == " __main__":
    app.run(host='0.0.0.0', debug=True) 
```

眼熟吗？我们不仅拿回了 Flask，还拿回了我们的整个应用程序工厂以及它所包含的一切。看一下 *application/__init__。py:*

```
from flask import Flask, g
from flask_sqlalchemy import SQLAlchemy
from flask_redis import FlaskRedis
from . import dashview

def create_app():
    """Construct the core application."""
    app = Flask( __name__ , instance_relative_config=False)
    app.config.from_object('config.Config')
    dash_app = dashview.Add_Dash(app)

    # Global Database and Session Cache
    db = SQLAlchemy()
    redis_store = FlaskRedis()

    with app.app_context():
        # Create the Application Context
        redis_store.uri = app.config['SQLALCHEMY_DATABASE_URI']
        redis_store.endpoint = app.config['ENDPOINT']
        db = SQLAlchemy(app)

        # Construct the data set
        from . import routes
        app.register_blueprint(routes.dashboard_blueprint)

        return app 
```

好像什么都没有改变！事实上，我们这里关于 Dash 的唯一一行是`dash_app = data.Add_Dash(app)`。

我们在`__init.py__`的开头导入‘dash view’。你可能会问，这是什么？它实际上是一个名为`dashview.py`的文件，位于我们的应用程序文件夹中。Dash 应用喜欢有单一的*。py* 文件，这对我们来说非常有用。让我们通过查看`dashview.py`来了解一下为什么会这样:

```
import glob
from dash import Dash
import dash_table
import dash_core_components as dcc
import dash_html_components as html
import pandas as pd

def Add_Dash(server):
    """Populates page with previews of datasets."""
    dash_app = Dash(server=server, url_base_pathname='/dataview/')
    dash_app.css.append_css({
        "external_url": "https://derp.sfo2.digitaloceanspaces.com/style.css"
        })

    # Create layout
    dash_app.layout = html.Div(
        id='flex-container'
      )

    return dash_app.server 
```

我们将 Flask 实例作为名为*服务器的参数传递给`Add_Dash`。*与之前的例子不同，这一次实际上是*服务器*在运行这个节目，Dash 作为一个模块被搭载。这是我们最重要的一行代码:

```
dash_app = Dash(server=server, url_base_pathname='/dataview/') 
```

Dash 不像 Flask 那样处理路由(或者根本不处理路由)。那也行！我们以 URL 前缀开始`dash_app`,这意味着这里的破折号逻辑仅限于这个页面。这意味着我们可以构建一个庞大的 Flask 应用程序，拥有数百个功能和视图，哦，是的，如果我们想要一个 Dash 视图，我们可以创建一个文件，让它自己冷却，不要接触任何其他东西。

现在，您正在考虑门户网站**。**

### 只是为了好玩

在这种情况下，我做了一件有趣的小事。在我们的文件 dashview 中，我让应用程序查看提取数据集的文件夹(名为 */datasets* )。对于每个数据集，我使用 Pandas 和 Flask 的数据表来创建导入到我们应用程序的每个数据集的预览。这让我们可以通过一个很酷的界面快速浏览应用程序所依赖的数据:

[![Cracking Full Control Over Plot.ly Dash](img/4df4a48f4a9b9514abeb0b7c5fc02286.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1B7gr704--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://res-3.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/dataframesgif.gif) 

<figcaption>有点粗糙，但你抓住了重点。</figcaption>

下面是我写的创建它的源代码:

```
import glob
from pathlib import Path, PurePath
from dash import Dash
import dash_table
import dash_core_components as dcc
import dash_html_components as html
import pandas as pd

p = Path('.')

def Add_Dash(server):
    """Populates page with previews of datasets."""
    dash_app = Dash(server=server, url_base_pathname='/dataview/')
    dash_app.css.append_css({
        "external_url": "https://derp.sfo2.digitaloceanspaces.com/style.css"
        })

    # Create layout
    dash_app.layout = html.Div(
        id='flex-container',
        get_datasets(),
      )

    return dash_app.server

def get_datasets():
    """Gets all CSVS in datasets directory."""
    data_filepath = list(p.glob('application/datasets/*.csv'))
    arr = []
    for index, csv in enumerate(data_filepath):
        print(PurePath(csv))
        df = pd.read_csv(data_filepath[index]).head(10)
        table_preview = dash_table.DataTable(
            id='table' + str(index),
            columns=[{"name": i, "id": i} for i in df.columns],
            data=df.to_dict("rows"),
            sorting=True,
        )
        arr.append(table_preview)
    return arr 
```

不用说，我们可以用 Plot.ly Dash 完成更酷的任务。停留足够长的时间，我们有机会涵盖所有这些。***