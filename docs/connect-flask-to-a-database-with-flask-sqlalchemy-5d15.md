# 用 Flask-SQLAlchemy 将 Flask 连接到数据库

> 原文：<https://dev.to/hackersandslackers/connect-flask-to-a-database-with-flask-sqlalchemy-5d15>

[![Connect Flask to a Database with Flask-SQLAlchemy](img/bd964c71a58577d44822a3aef41d962a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QMa0KYij--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hackersandslackers-cdn/2019/10/flask-sqlachemy.jpg)

到目前为止，您肯定已经熟悉了 Python 的 [SQLAlchemy 库](https://dev.to/hackersandslackers/pythonic-database-management-with-sqlalchemy-37m3)的好处:这是一个基本上适用于任何数据库相关内容的一体化解决方案。像大多数主要的 Python 库一样，SQLAlchemy 已经被移植到一个专门与 Flask 兼容的版本中，恰当地命名为 **Flask-SQLAlchemy** 。

与核心的 SQLAlchemy 包类似，Flask-SQLAlchemy 为我们提供了一个 ORM，通过轻松创建定义的模型来修改应用程序数据。不管您选择的数据库是什么，Flask-SQLAlchemy 将确保我们用 Python 创建的模型能够翻译成我们选择的数据库的语法。鉴于 Flask-SQLAlchemy 的易用性和一刀切的性质，难怪该库从一开始就是 Flask 事实上的数据库库(说真的，还有其他选择吗？)

## 为您的应用配置 Flask-SQLAlchemy

在与数据库交互之前，我们需要预先设置一些基本的配置变量。作为标准，我们将使用在`config.py`中定义的类来处理我们的烧瓶配置:

<figure>

```
from os import environ

class Config:
    """Set Flask configuration vars from .env file."""

    # General
    TESTING = environ["TESTING"]
    FLASK_DEBUG = environ["FLASK_DEBUG"]
    SECRET_KEY = environ.get('SECRET_KEY')

    # Database
    SQLALCHEMY_DATABASE_URI = environ.get("SQLALCHEMY_DATABASE_URI")
    SQLALCHEMY_TRACK_MODIFICATIONS = environ.get("SQLALCHEMY_TRACK_MODIFICATIONS") 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>config.py</figcaption>

</figure>

这里有两个特定于 Flask-SQLAlchemy 的变量:

`SQLALCHEMY_DATABASE_URI`:我们需要连接到数据库的连接字符串。这遵循标准惯例:`[DB_TYPE]+[DB_CONNECTOR]://[USERNAME]:[PASSWORD]@[HOST]:[PORT]/[DB_NAME]`

*   `SQLALCHEMY_ECHO`:当设置为‘True’时，Flask-SQLAlchemy 将把所有数据库活动记录到 Python 的 stderr 中，以便调试。
*   老实说，我总是把这个设置为“假”，否则每次你运行应用程序时都会出现一个讨厌的警告，提醒你这个选项会占用很多系统资源。

这些才是我们应该担心的大问题。如果你进入了一些下一级的数据库，有一些其他的专业模式配置变量，你可以在这里找到。

通过对上述变量使用精确的命名约定，只需将它们放在我们的配置文件中，就会自动为我们配置数据库连接。我们永远不需要创建引擎、会话或连接。

## 启动烧瓶-化学滴定

和往常一样，我们将使用 [Flask 应用程序工厂方法](https://hackersandslackers.com/structuring-your-flask-app/)来启动我们的应用程序。如果你对这个术语不熟悉，你会发现这个教程令人困惑，而且几乎没用。

使用 Flask-SQLAlchemy 的 Flask 应用程序的最基本的`__init__.py`文件应该是这样的:

<figure>

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

def create_app():
    """Construct the core application."""
    app = Flask( __name__ , instance_relative_config=False)
    db.init_app(app)
    app.config.from_object('config.Config')

    with app.app_context():
        # Imports
        from . import routes

        # Create tables for our models
        db.create_all()

        return app 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>init.py</figcaption>

</figure>

注意`db`的存在及其位置:这个数据库对象被设置为`create_app()`之外的*全局*变量。另一方面，`create_app()`的内部包含了线`db.init_app(app)`。即使我们已经全局地设置了 db 对象，这也没有任何意义，除非我们在创建应用程序后初始化它。我们通过在`create_app()`内调用`init_app()`，并将我们的应用程序作为参数传递来实现这一点。在实际的“应用程序上下文”中，我们将调用`create_all()`，稍后我们将对此进行介绍。

如果你觉得最后一段完全是胡言乱语，那么你并不孤单。Flask Application Factory 可能是 Python 软件开发中最奇怪、最难解释的概念之一——我最好的建议是不要灰心丧气，采用上面的复制+粘贴代码，盲目地接受足够多次的填鸭式胡说八道，直到它成为第二天性。这就是我所做的，即使我完成了这个教程，我*仍然*遇到了令人讨厌的怪癖，让我措手不及。

请注意我们在名为 **routes** 的应用程序上下文中进行的导入。这是我们还没有写的两个文件中的一个:一旦我们创建了它们，我们的应用程序文件结构将看起来像这样:

<figure>

```
my-app
├── /application
│ ├── __init__.py
│ ├── routes.py
│ ├── models.py
├── .env
├── config.py
└── wsgi.py 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Our app structure</figcaption>

</figure>

## 创建数据库模型

在我们的应用程序目录中创建一个`models.py`文件。这里我们将导入我们在`__init__.py`中创建的`db`对象。现在我们可以通过在这个文件中定义类来创建数据库模型。

一个常见的例子是从一个**用户**模型开始。我们创建的第一个变量是`__tablename__`，它将对应于新用户将被保存的 SQL 表的名称。我们在这个模型类中创建的每个额外变量都将对应数据库中的一列:

<figure>

```
"""Data models."""
from . import db

class User(db.Model):
    """Data model for user accounts."""

    __tablename__ = 'flasksqlalchemy-tutorial-users'
    id = db.Column(
        db.Integer,
        primary_key=True
    )
    username = db.Column(
        db.String(64),
        index=False,
        unique=True,
        nullable=False
    )
    email = db.Column(
        db.String(80),
        index=True,
        unique=True,
        nullable=False
    )
    created = db.Column(
        db.DateTime,
        index=False,
        unique=False,
        nullable=False
    )
    bio = db.Column(
        db.Text,
        index=False,
        unique=False,
        nullable=True
    )
    admin = db.Column(
        db.Boolean,
        index=False,
        unique=False,
        nullable=False
    )

    def __repr__(self):
        return '<User {}>'.format(self.username) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>models.py</figcaption>

</figure>

每个“列”接受以下属性:

*   **数据类型:**接受下列之一:`String(size)`、`Text`、`DateTime`、`Float`、`Boolean`、`PickleType`或`LargeBinary`。
*   **primary_key** (可选):当设置为 **True** 时，该字段将作为相应数据库表中的主键。
*   **foreign_key** (可选):设置与另一个表中的字段的外键关系。目标列必须有一个**唯一**约束，以便在两个表之间建立关系。
*   **unique** (可选):当设置为 **True** 时，该字段将在数据库中添加一个唯一约束。
*   **nullable** (可选):** **接受一个布尔值，该值指定该列是否可以接受空值。

随着我们第一个模型的创建，您已经比想象中更接近与数据库的交互了。

**Protip:** SQLAlchemy 模型有一些保留的变量名，可用于进一步配置相应的数据库表。这里有几个例子:

*   `__tablename__`:设置结果表的名称。
*   `__table_args__ = {"schema": "my_name"}`:设置保存表格的 Postgres 模式。
*   `__table_args__ = {'extend_existing': True}`:指定目标数据库中已经存在同名的表。设置此项后，您的模型将不会覆盖现有的表，因此，您的模型中的字段可能与您的表中的列不匹配。

## 创建我们的第一个用户

随着我们的**用户**模型的创建，我们现在有了在数据库中动态创建用户所需的东西。这里我们将打开`routes.py`并创建一个简单的路径来创建一个用户。这个路由将接受两个查询字符串参数:`?user=`将接受一个用户名，`&email=`将接受一个电子邮件地址:

<figure>

```
from flask import request, render_template, make_response
from datetime import datetime as dt
from flask import current_app as app
from .models import db, User

@app.route('/', methods=['GET'])
def create_user():
    """Create a user."""
    username = request.args.get('user')
    email = request.args.get('email')
    if username and email:
        new_user = User(
            username=username,
            email=email,
            created=dt.now(),
            bio="In West Philadelphia born and raised, on the playground is where I spent most of my days",
            admin=False
        ) # Create an instance of the User class
        db.session.add(new_user) # Adds new User record to database
        db.session.commit() # Commits all changes
    return make_response(f"{new_user} successfully created!") 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>routes.py</figcaption>

</figure>

看看这有多简单！创建一个用户所要做的就是创建一个 user 类的实例，通过`db.session.add(new_user)`将其添加到我们的会话中，然后用`db.session.commit()`提交更改！这就是用 SQLAlchemy 这样的数据库 ORM 来驱动应用程序的吸引力所在:我们现在可以像管理对象一样管理数据，而不用费心处理 SQL 语句。在构建应用程序的背景下，数据模型为我们提供了一种安全、简单的方法来修改数据。

变量 **new_user** 在内存中本地创建一个**用户**的实例。我们传递关键字参数来设置我们的类中每个字段的值(*用户名*、*电子邮件*、*创建*和*简历*)。

`db.session.add(new_user)`暂存我们刚刚创建的要添加到数据库中的新用户。与 Python 中的任何数据库交互一样，在我们明确给出 OK 之前，不会实际提交更改。在 Flask-SQLAlchemy 中，这是通过`db.session.commit()`完成的。

## 查询我们的新数据

我们已经创建了一个用户，但是我们如何确保不会创建重复的记录呢？由于*用户名*和*电子邮件*字段有**唯一的**约束，我们的数据库在技术上不允许这种情况发生。如果我们不添加适当的验证，试图在我们的表中插入多个唯一值将导致应用程序崩溃。与其崩溃，不如让我们构建一个漂亮的错误消息！

<figure>

```
from flask import request, render_template, make_response
from datetime import datetime as dt
from flask import current_app as app
from .models import db, User

@app.route('/', methods=['GET'])
def create_user():
    """Create a user."""
    username = request.args.get('user')
    email = request.args.get('email')
    if username and email:
        existing_user = User.query.filter(User.username == username or User.email == email).first()
        if existing:
            return make_response(f'{username} ({email}) already created!')
        new_user = User(
            username=username,
            email=email,
            created=dt.now(),
            bio="In West Philadelphia born and raised, on the playground is where I spent most of my days",
            admin=False
        ) # Create an instance of the User class
        db.session.add(new_user) # Adds new User record to database
        db.session.commit() # Commits all changes
    return make_response(f"{new_user} successfully created!") 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>routes.py</figcaption>

</figure>

在我们创建一个新用户之前，`existing_user`变量查询我们的用户表，看看是否已经存在一个记录，其中包含所提供的信息。为了查询属于一个数据模型的记录，我们编写这样一个语句:

```
[MODEL].query.[METHOD_1].[METHOD_1].[FIRST or ALL] 
```

Enter fullscreen mode Exit fullscreen mode

`[MODEL]`是我们的数据模型类名，我们用`.query`跟踪它。现在，我们可以使用多种方法来构造查询:

*   `filter([CRITERIA])`:返回符合给定条件的所有记录。
*   `order_by([MODEL].[COLUMN])`:按列对返回的记录进行排序。
*   `get([VALUE])`:根据主键检索记录。
*   `.limit([INTEGER])`:设置最大检索记录数。

在我们的例子中，我们使用`.filter()`方法来检索已经拥有我们想要的用户名或电子邮件的现有用户(我们在查询中使用`first()`而不是`all()`，因为我们期望最多有 1 条记录)。如果记录存在，我们向用户传递一个错误:

```
myuser (myuser_972@example.com) already created! 
```

Enter fullscreen mode Exit fullscreen mode

### 加载记录到模板中

对于我们的最后一招，让我们看看我们的金贾随着数据库记录播放。我修改了下面的路径来渲染一个名为**users.html**的模板。该模板将创建一个列出所有现有用户的 HTML 页面:

<figure>

```
...

@app.route('/', methods=['GET'])
def create_user():
    """Create a user."""
    ...
    users = User.query.all()
    return render_template(
        'users.html',
        users=users,
        title="Show Users"
    ) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>routes.py</figcaption>

</figure>

语句`User.query.all()`将返回数据库中`User`的所有实例*。我们用`users=users`将所有这些记录传递到我们的模板中。这是我们的 Jinja 模板:* 

<figure>

```
{% extends "layout.html" %}

{% block content %}
  {% for user in users %}
    <ul id="user.username">
      <li>Username: {{ user.username }}</li>
      <li>Email: {{ user.email }}</li>
      <li>Created: {{ user.created }}</li>
      <li>Bio: {{ user.bio }}</li>
      <li>Admin: {{ user.admin }}</li>
    </ul>
  {% endfor %}
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>users.html</figcaption>

</figure>

当我们启动应用程序时，我们应该会在主页上看到以下内容:

<figure>[![Connect Flask to a Database with Flask-SQLAlchemy](img/1048cfe8416ee8f689171561ee87d5b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mj9BFko_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hackersandslackers-cdn/2019/09/flasksqlalchemy-test.png) 

<figcaption>我们已经升空了！</figcaption>

</figure>

看起来我们可以显示现有用户了！让我们再添加一些记录，以确保一切正常:

<figure>[![Connect Flask to a Database with Flask-SQLAlchemy](img/f551efcf1ddc00635309ebfdf4187cb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iwc7JdZT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-1.cloudinary.com/hackers-and-slackers/image/upload/f_auto%2Cq_auto/img/Screen-Shot-2019-02-06-at-12.39.07-AM.png) 

<figcaption>我就不信这不是错误信息。</figcaption>

**T8】**</figure>

 **### 来，把我的东西都拿走

一旦你开始，Flask-SQLAlchemy 是很棒的，但是正如我们已经看到的,“设置”并不总是在公园里散步。这是那些无论你凭记忆做了多少次似乎总是错的事情之一。

作为临别礼物，我把这个教程的源代码放到了 Github 上，供你珍藏和欣赏。不，说真的，拿着。把它拿走。我今天不喝烧瓶了。我需要去打火箭联赛。

**PS:在 PSN 上加我**

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[烧瓶-sqlalchemy-教程](https://github.com/hackersandslackers/flask-sqlalchemy-tutorial)

### 🐍ℹ️通过一个 SQL 数据库在你的 Flask 应用程序中创建和管理数据。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 烧瓶-化学教程

[![Python](img/91eac0264bc3c2568bdd56361da1d1ae.png)](https://camo.githubusercontent.com/0b153563dd844ea8bbae9bf9d219c0d9930aa35e7d2ed0e07638a2caa3745902/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f507974686f6e2d76253545332e382d626c75652e7376673f6c6f676f3d707974686f6e266c6f6e6743616368653d74727565266c6f676f436f6c6f723d776869746526636f6c6f72423d356538316163267374796c653d666c61742d73717561726526636f6c6f72413d346335363661)[![Flask](img/c488dcbd4bdf0afa3aa18515e5c7b32c.png)](https://camo.githubusercontent.com/f084d311ceb36b7defc164ff0bdeac736439789c62fd77f1c595eea119f9c51f/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f466c61736b2d76322e312e312d626c75652e7376673f6c6f6e6743616368653d74727565266c6f676f3d666c61736b267374796c653d666c61742d737175617265266c6f676f436f6c6f723d776869746526636f6c6f72423d35653831616326636f6c6f72413d346335363661)[![Flask-SQLAlchemy](img/dd06be317b07dc2ff48e28fbfc160a71.png)](https://camo.githubusercontent.com/90c3bef49f922816be917d5b8f9ceb4b35f2d2e879127d19edea8c60a903aea5/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f466c61736b2d2d53514c416c6368656d792d322e352e312d7265642e7376673f6c6f6e6743616368653d74727565267374796c653d666c61742d737175617265266c6f676f3d666c61736b266c6f676f436f6c6f723d776869746526636f6c6f72413d34633536366126636f6c6f72423d356538316163)[![GitHub Last Commit](img/ee0945856a39b3a04347e6b66485ab1c.png)](https://camo.githubusercontent.com/0335ba6462162e24c13d82040759cb5fa55a13e5ce2841406e62e5f052bdfff0/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6173742d636f6d6d69742f676f6f676c652f736b69612e7376673f7374796c653d666c61742d73717561726526636f6c6f72413d34633536366126636f6c6f72423d613362653863)[![GitHub Issues](img/a9bb83288c57b59cdf273d766f4cf674.png)](https://github.com/hackersandslackers/flask-sqlalchemy-tutorial/issues)[![GitHub Stars](img/c135ed3fffa1e0b674c95d85354d8624.png)](https://github.com/hackersandslackers/flask-sqlalchemy-tutorial/stargazers)[![GitHub Forks](img/bc0d81c1f7fa4dfc3af525447051fca6.png)](https://github.com/hackersandslackers/flask-sqlalchemy-tutorial/network)

[![Flask-SQLAlchemy Tutorial](img/07e8719c2041912075aab2d18e3fccbb.png)](https://github.com/hackersandslackers/flask-sqlalchemy-tutorial/blob/master/.github/flask-sqlachemy@2x.jpg?raw=true)

使用 Flask-SQLAlchemy 将 Flask 应用程序连接到数据库。

*   **教程**:[https://hackersandslackers . com/manage-database-models-with-flask-sqlalchemy/](https://hackersandslackers.com/manage-database-models-with-flask-sqlalchemy/)

*   **演示**:[https://flasksqlanche . hackersandslackers . app](https://flasksqlalchemy.hackersandslackers.app)

# 入门指南

分两步在本地进行设置:

### 环境变量

用您的值替换 **.env.example** 中的值，并将该文件重命名为**。环境**:

*   `FLASK_APP`:您的应用程序的入口点；应该是`wsgi.py`。
*   运行应用程序的环境；不是`development`就是`production`。
*   `SECRET_KEY`:随机生成的字符串，用于加密应用程序的数据。
*   `SQLALCHEMY_DATABASE_URI` : SQLAlchemy 连接 URI 到一个 SQL 数据库。

记住永远不要提交保存在。Github 的 env 文件。

### 装置

使用`make deploy`开始运行:

```
$ git clone https://github.com/hackersandslackers/flask-sqlalchemy-tutorial.git
$ cd flask-sqlalchemy-tutorial
$ make deploy
```

Enter fullscreen mode Exit fullscreen mode

* * *

**黑客和懒鬼**教程免费。如果你觉得这个教程有帮助，一个[的小额捐赠](https://www.buymeacoffee.com/hackersslackers)将会非常感谢，让我们继续做生意。所有收益归…

</article>

[View on GitHub](https://github.com/hackersandslackers/flask-sqlalchemy-tutorial)**