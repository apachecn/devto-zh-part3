# 使用 Flask 构建您的第一个 Python Web 应用程序

> 原文：<https://dev.to/bhavaniravi/build-your-1st-python-web-app-with-flask-4oo7>

使用 Python 构建您的第一个 web 应用程序的分步教程。

### 为什么选这个教程？

我经常有冲动去写一些关于基础 101 主题的操作方法，因为我强烈地感觉到互联网上已经有足够多的内容了。

然而，在看到人们阅读一个又一个教程，观看一个又一个视频后，我注意到他们仍然不觉得*“他们足够好”或“他们知道足够多”*可以构建一个行业级应用程序。这些课程将带你进入一个结构化的环境，在这里犯错的可能性较小。因此，当放在实时环境中构建自己的应用程序时，您通常不知道从哪里开始。

在这篇博客中，我将带你一步一步地学习如何使用 Flask 构建一个待办事项应用程序。我们不仅要一起编码，还要思考和解决我们遇到的问题。

但是如果您已经了解 Flask，并且只想看一下代码。给你。

### 非技术要求

1.  你的时间

既然你已经登陆了这个博客，你需要一些时间来完成它。如果你现在没有时间，把这个博客加入书签，等你有时间的时候再回来。相信我，通读/浏览不会有任何帮助。

1.  笔记本电脑/电脑——在手机上编码是完全不可能的

2.  一个安静的地方——不仅仅是为了这个博客；学习任何东西都需要你全神贯注。所以确保你在一个较少分心的地方。

### 为什么是 Python？

Python 是初学者最喜欢的语言，以其简单性和简洁明了的实现方式而闻名。尽管围绕机器学习大肆宣传，Python 也已经做了足够多的工作来征服 web 应用程序领域。

它是构建后端应用程序最受欢迎的语言之一。即使面对优秀的老 PHP 和全栈 Javascript，Python 也有它的位置，因为它易于实现，并且有一个支持性的社区让初学者着迷。

### 为什么是烧瓶？

就像人们对从哪个 Python 版本开始感到困惑一样，Django vs Flask 是另一个古老的争论。虽然人们说 Flask 简单易用，而 Django 对于构建 web 应用程序来说很笨重，但是还有一个重要的原因让你应该选择 Flask 而不是 Django。

作为云时代的开发人员，我们正在远离单一的应用程序。有了微服务架构，运行多个 Django 服务器将会使您的服务变得繁重，因为它附带了所有内置的应用程序。

[![](img/2699a528ec275408d2ad7afd5e8db7e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hDFMMf6w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/973/1%2Ae2v4HCTyZo8bQqDU7iZqMw.png)

### 需求&设置

1.  当然，我们需要[Python——3.7 会很棒](https://www.python.org/downloads/)
2.  Pycharm 社区 IDE，因为它很棒&免费
3.  有了 IDE 之后，创建一个项目。我把我的命名为托多烧瓶。多么有创意！！
4.  virtualenv——如果你使用 Pycharm，你不必担心这个问题。
5.  使用 pip 安装烧瓶安装烧瓶

如果你知道 Flask 的基本知识，并且想直接开始开发这个应用程序。j [*ump 这里。*](https://medium.com/p/b039d11f101c#4d5a)

### 你好世界

让我们通过编写一个“hello world”程序来检查我们的设置是否正常。在项目下创建一个 app.py 文件。除了将文件 flask.py 命名为 flask . py 之外，您可以将它命名为任何名称，因为这将与我们安装的实际 flask 包产生冲突。

```
todo-flask
    |\_ app.py 
```

密码。通读每一行，因为每一行都附有解释

```
# app.py

from flask import Flask # import flask
app = Flask(\_\_name\_\_) # create an app instance

@app.route("/") # at the end point /
def hello(): # call method hello
    return "Hello World!" # which returns "hello world"

if \_\_name\_\_ == "\_\_main\_\_": # on running python app.py
    app.run() # run the flask app 
```

### **运行应用程序**

通过运行`app.py`文件来运行应用程序。默认情况下，flask 在端口 5000 运行本地服务器。

```
python app.py 
```

[![](img/3f0959fb0bf528bd7c70a93279e2fe87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iu9iOpfG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/771/1%2AGi4WOj-oKXJDa4kNGnjF6Q.png)

从你的浏览器点击网址，你会看到你的第一个 hello world 程序。

[![](img/04cb966aacb549f9ee2358c38cafa57f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tV9Mhj0Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/440/1%2A7PAiHXhwFNkl2EDt9Whlhw.png)

### **你好你的名字**

```
@app.route("/<name>") # at the end point /<name>
def hello\_name(name): # call method hello\_name
    return "Hello "+ name # which returns "hello + name 
```

让我们点击[http://localhost:5000/bhava](http://localhost:5000/bhava)。我们将得到一个 404 错误。

#### 调试模式

这是因为我们在生产模式下运行服务器。出于开发目的，我们使用一种叫做调试模式的东西。当 debug=Trueis 设置时，当我们向 Flask 应用程序添加新代码时，服务器重新启动。为了设置调试模式，请执行以下操作

1.  将 app.run()行修改为 app.run(debug=True)。
2.  停止正在运行的服务器，然后重新启动它。

您将看到“调试器处于活动状态”，这意味着调试模式已经生效。现在你可以继续编辑你的文件了，服务器将会重启。

[![](img/0bf565311652c32430297f0eb623a385.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IySvwvc7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/782/1%2AncRr9sW61bMFDmK3Jzat8Q.png)

### 让我们构建待办事项列表应用程序

现在您已经准备好了设置并开始工作，让我们直接进入并开始构建我们的应用程序。你可能会迫不及待地一头扎进去，开始编码。我也是。让我们一起控制这种冲动一段时间，做一些计划。

#### 用户可以做什么？

假设您正在构建这个应用程序，并将其提供给用户。你期望他们做哪些操作？

1.  创建项目
2.  删除项目
3.  将项目标记为完成
4.  更新项目

这些是使它成为一个待办事项应用程序的基本功能。但你可以去幻想的功能，并提供

1.  将待办事项分类为工作、个人等。,
2.  标记项目
3.  排列项目优先级
4.  项目在预计时间内未完成时的剩余量

#### **数据看起来怎么样？**

既然我们已经弄清楚了需要执行的操作，那么让我们来看看每个操作的数据是什么样子的

1.  **创建项目—** 标题、描述、创建日期、到期日期
2.  **删除项目** — _is_deleted

现在我们要决定你是想永久删除记录，还是设置一个标志表示它已被删除。这被称为软删除。软删除在面向客户的系统中是一种常见的做法。

**3。将项目标记为完成** — _is_done

1.  **更新项目** —除了我们创建的信息，我们不编辑任何其他信息。所以这里没有额外的参数。

#### **设计模式**

对于我们的数据库，我们将使用 SQLite 关系数据库，这意味着我们将以表的形式存储数据。

我们需要一个表来存储用户创建的 ToDo 列表，这是我们的第一个包含列的表。

```
ToDo\_Items 
- Id Primary Key
— Title Text
- Description Text  
- CreatedOn Date
- DueDate Date
- \_is\_deleted Boolean
- \_is\_done Boolean 
```

现在，如果我们只为一个人设计一个系统，这看起来不错，但我们不是。我们将把它交给很多人，这意味着我们需要一个注册进入系统的所有用户的列表，并维护一个与我们的 ToDo items 表中的每个 ToDo 项相关联的用户的引用。

```
User
- Name Text
- Email Email
- Id Primary Key

ToDo\_Items
   ...
   ...
   CreatedBy ForeginKey(User) 
```

#### 构建我们的代码

初学者常犯的一个错误是将所有代码放入一个文件中。虽然这是完全可以接受的，因为你是一个初学者，但了解最佳实践以及我们为什么这样做总是好的。这会给你一种构建真正软件的感觉，而不是构建一个快速的黑客。

现在对于我们的应用程序，我们将把代码分成 3 个部分

1.  app.py —我们的应用程序的入口和出口点
2.  service.py —将请求转换为响应。
3.  models.py —处理涉及数据库的所有事情。

#### **为什么我们需要 3 个独立的文件？**

**标准—** 初学者教程中遗漏的一件重要事情是需要遵循的标准或模式。通过将应用程序分成 3 个不同的文件，我们将业务逻辑(service.py)、应用层(app.py)和数据(models.py)分开。用技术术语来说，这被称为**MVC——模型视图控制器模式。**

**代码可维护性—** 假设几年后，您认为 MongoDB 比 Sqlite 更好，并决心改变它。在一个单一的文件应用程序中，你几乎要摆弄文件的每一行。但是有了我们的 MVC 模型，您需要做的就是转储 models.py 文件并使用一组 MongoDB 查询重写它。

### 让我们把手弄脏吧

从现在开始，我们将直接跳到我们的代码上，边走边学。在我们继续之前，让我们快速浏览一下 SQLite 的初级读本。

```
# 1.import sqlite
import sqlite

# 2\. create a connection to DB     
conn = sqlite3.connect('todo.db')

# 3\. Write your sql query   
query = "<SQLite Query goes here>"

# 4\. execute the query
result = conn.execute(query) 
```

在整个 models.py 文件中，您可以看到这种模式在我们的 todo 列表中重复创建、删除和更新项目。

#### 创建表格

让我们暂时把 Flask 放在一边，看看 Pythonic 创建这些表的方式。为了处理所有与数据库相关的操作，我们将创建一个名为 models.py 的单独文件

它由两部分组成

1.  **模式——在哪里创建和维护*数据库表*。**

```
class Schema:
    def \_\_init\_\_(self):
        self.conn = sqlite3.connect('todo.db')
        self.create\_user\_table()
        self.create\_to\_do\_table()
        # Why are we calling user table before to\_do table
        # what happens if we swap them?

    def **create\_to\_do\_table** (self):

        query = """
        CREATE TABLE IF NOT EXISTS "Todo" (
          id INTEGER PRIMARY KEY,
          Title TEXT,
          Description TEXT,
          \_is\_done boolean,
          \_is\_deleted boolean,
          CreatedOn Date DEFAULT CURRENT\_DATE,
          DueDate Date,
          UserId INTEGER FOREIGNKEY REFERENCES User(\_id)
        );
        """

        self.conn.execute(query)

    def create\_user\_table(self):
        # create user table in similar fashion
        # come on give it a try it's okay if you make mistakes
        pass 
```

现在，这些表必须在您启动应用程序时创建，这意味着它将在我们的 app.run 命令之前被触发。

```
if \_\_name\_\_ == "\_\_main\_\_":
    Schema()
    app.run(debug=True) 
```

**2。Todo —与 Todo 表**相关的操作

您可以将模型视为将 DB 表与相关函数相关联的绑定。因此，我们创建了一个具有 4 个方法创建、更新、删除和列表的类，对应于 4 个常见的 SQL 查询创建、更新、删除和选择

```
class ToDoModel:
    TABLENAME = "TODO"

    def \_\_init\_\_(self):
        self.conn = sqlite3.connect('todo.db')

    def **create** (self, text, description):
        query = f'insert into {TABLENAME} ' \
                f'(Title, Description) ' \
                f'values ("{text}","{description}")'

        result = self.conn.execute(query)
        return result
   # Similarly add functions to select, delete and update todo 
```

### 服务方式

我们将视图方法从服务方法中分离出来，因为它使您能够轻松地测试这些功能。

```
**# Service.py**
class ToDoService:
    def \_\_init\_\_(self):
        self.model = ToDoModel()

    def create(self, params):
        self.model.create(params["text"], params["Description"]) 
```

### 查看功能

我已经提到的视图功能是系统的入口和出口点。我们在该文件中做出的决策包括

1.  我们期望用户输入的类型——JSON、文件、自由文本等。,
2.  我们向用户提供的输出类型——JSON、文件、HTML 页面等。,
3.  请求的认证
4.  记录请求

```
# app.py

@app.route("/todo", **method=["POST"]**)
def **create\_todo** ():
    return ToDoService().create(request.get\_json()) 
```

### 最终魔法

现在，我们所有的功能都已经就绪，可以相互通信了。我们所要做的就是点击 API 端点，创建一些 to do 项，并看到它全部工作。

在这篇博客中，我从来没有谈到我们如何为人们建立一个界面来输入待办事项，以及他们在哪里标记它们完成等等。正如我所说，在开始时，我们不仅学习 Flask，还学习行业中遵循的惯例。

大多数软件的后端和前端是完全分离的。我们所创建的是一个后端系统。

为了看到它的工作，我们可以构建一个前端，我们将在下一篇博客中使用 React 来实现。但是现在，我将向您展示如何使用 Python 或 Postman 来使用它。

1.  让您的 app.py 在一个控制台上运行。现在，正如您所看到的，它正在我的本地主机的端口 5000 上运行。

[![](img/d411a47f93684443b1e961272bafadbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yfDcDsB9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/832/1%2A1CVwHmR-pHctg6r8Szzvow.png)

1.  让我们创建我们的第一个待办事项。打开另一个终端。我用请求 python 包用 Python 消费 API。你也可以用邮递员

```
pip install requests 
```

1.  打开 python shell，点击 API

```
$ python
>>> import requests
>>> requests.post("[http://localhost:5000/todo](http://localhost:5000/todo)", 
                  json={"Title":"my first todo", 
                        "Description":"my first todo"})
<Response [200]> 
```

requests.post 用创建 ToDo 项所需的数据调用我们刚刚创建的 post API。我们得到一个响应 200，这意味着它已成功创建。

我已经运行了代码 4 次，在使用 GET 方法点击/todo API 时，我得到了以下响应。

[![](img/85cfbd451e2da1cfd1610b86e2e62390.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MDKTXflT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/584/1%2AqaBVROHx0yxrOQ75-f5Igg.png)

如果你已经在博客上走了这么远，给自己一个鼓励，因为你猜怎么着？你太棒了。这只是起点。GitHub 上有整个工作库。T3】

在接下来的博客中，我们将会学到很多关于构建软件的知识。接下来我们将播放

1.  如何用 ReactJS 为这个 ToDo 应用程序构建一个前端
2.  如何为我们的 Python 后端编写单元测试
3.  如何将我们的申请归档
4.  如何部署我们的整个应用程序，并让人们可以使用它
5.  还有更多…

你喜欢你读到的东西吗？

***按住*** *的“鼓掌”键，给我一个* ***大喊*** *上*【* * _ 推特** *】([https://twitter.com/@bhavaniravi](https://twitter.com/@bhavaniravi))*。_

* * *