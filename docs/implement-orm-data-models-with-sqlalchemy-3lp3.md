# 用 SQLAlchemy 实现 ORM 数据模型

> 原文：<https://dev.to/hackersandslackers/implement-orm-data-models-with-sqlalchemy-3lp3>

[![Implement ORM Data Models with SQLAlchemy](img/913721ee46dbd51ee6ac5efcebe795f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HdDGAii0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers-cdn.storage.googleapis.com/2021/01/sqlalchemy-models.jpg)

利用 ORMs 作为数据层是一个和面向对象编程本身一样古老的概念；通过抽象 SQL 概念，开发人员通过修改对象而不是查询来避免可怕的“上下文切换”。ORM 不仅仅是过度热情的 OOP 时代慢慢消失的产物；它们通过确保应用程序端的数据完整性来保证一定程度的持久性，从而最小化灾难性 SQL 查询的可能性。

ORM 允许开发人员通过修改代码中的对象(数据类)来处理数据，而不是对数据库执行 SQL 查询。这种工作流为面向用户的应用程序带来了回报，这些应用程序定期验证和修改数据(比如验证用户身份、修改配置文件、发布内容等)。).ORMs 通过提供一个接口来处理频繁且可预测的操作而大放异彩；这对*应用开发*来说是必不可少的，但是对于任何涉及*数据分析*的事情来说肯定是个负担。

## 创建模型

数据模型是表示数据库中 SQL 表的 Python 类，其中模型的属性转化为表中的列。

当使用 ORM 时，创建我们的**模型**的**实例**转化为在 SQL **表**中创建**行**。自然，这意味着我们需要在编写任何有意义的业务逻辑之前定义我们的模型。

我们通过定义 Python 类来创建模型，这些 Python 类从 SQLAlchemy 扩展了一个叫做`declarative_base()`的东西。我们这样定义一个模型“基础”对象:

<figure>

```
"""SQLAlchemy Data Models."""
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>models.py</figcaption>

</figure>

现在我们可以扩展`Base`来创建我们的第一个模型。按照传统，我们的第一个模型将是一个恰当地命名为**用户**的用户账户模型。我们从简单的开始:

<figure>

```
"""SQLAlchemy Data Models."""
from sqlalchemy import Column
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.types import Integer, Text, String

Base = declarative_base()

class User(Base):
    """User account."""

    __tablename__ = "user"

    id = Column(
        Integer,
        primary_key=True,
        autoincrement="auto"
    )
    username = Column(
        String(255),
        unique=True,
        nullable=False
    )
    password = Column(
        Text,
        nullable=False
    ) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>models.py</figcaption>

</figure>

虽然它可能是一个准系统，但上面的 Python 类是一个完全有效的数据模型，它将产生一个包含三列(每个属性一列)的表: **id** 、**用户名**和**密码**。每个属性都有类型`Column()`，这是 SQLAlchemy 特有的数据结构(因此我们包含了`from sqlalchemy import Column`)。

我们还导入了三个 SQLAlchemy“类型”，我们看到它们被传递给了每个`Column`。每个**类型**对应一个 SQL 数据类型。因此，我们的 SQL 表列的数据类型将分别是**整数**、 **varchar(255)** 和**文本、**。

还可以接受可选参数，如键或列约束:

*   **primary_key** :将一列指定为表的“主键”，这是一种强烈推荐的做法，既可以作为惟一标识符，也可以作为 SQL 搜索的索引。
*   **自动增量**:仅与既`primary_key`又有类型`Integer`的列相关。我们创建的每个用户都会自动分配一个 id，其中我们的第一个用户会有一个 id**1**，随后的用户会相应增加。
*   **unique** :在给定的列中，没有任何两个记录/行共享相同的值(我们不希望两个用户有相同的用户名)的情况下设置约束。
*   **可空的**:当设置为`True`时，添加一个约束，该列是强制的，除非提供一个值，否则不会创建任何行。
*   **key** :在给定的列上放置一个辅键，通常与另一个约束(如“index”)一起使用。
*   **index** :指定为了提高查询性能，列的值可以以非任意的方式排序
*   **server_default** :如果没有显式传递值，则指定默认值。

在我们的例子中，我们设置可选属性`__tablename__`来明确指定应该命名哪个模型的对应 SQL 表。如果不存在，SQL 将使用类名来创建表。

有了这些知识，我们可以继续构建我们的模型:

<figure>

```
...
from sqlalchemy.types import Integer, Text, String, DateTime
from sqlalchemy.sql import func

class User(Base):
    """User account."""

    __tablename__ = "user"

    id = Column(
        Integer,
        primary_key=True,
        autoincrement="auto"
    )
    username = Column(
        String(255),
        unique=True,
        nullable=False
    )
    password = Column(
        Text,
        nullable=False
    )
    email = Column(
        String(255),
        unique=True,
        nullable=False
    )
    first_name = Column(String(255))
    last_name = Column(String(255))
    bio = Column(Text)
    avatar_url = Column(Text)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, server_default=func.now())

    def __repr__ (self):
        return "<User %r>" % self.username 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>models.py</figcaption>

</figure>

现在那是一个模型！我们在模型中添加了一些属性，每个属性都是不言自明的。`created_at`和`updated_at`列演示了如何使用 SQLAlchemy 函数自动分配这些值。

为了记录或调试我们的类实例，最好在数据模型(以及一般的 Python 类)上设置`__repr__`的值。当我们`print()`一个`User`的实例时，`__repr__`返回的值就是我们将看到的。如果你曾经在 Javascript 中处理过`[object Object]`,你就会知道调试一个对象的值却得不到任何有用的回报是多么令人讨厌。

我们的模型看起来不错，所以让我们用它创建一个 SQL 表。在我们的模型被创建之后，我们通过调用一个名为`create_tables()`的方法来做到这一点:

<figure>

```
Base.metadata.create_all(engine) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Create SQL tables from classes which extend `Base`</figcaption>

</figure>

一旦运行，SQLAlchemy 将处理数据库端的一切，创建一个与我们的模型相匹配的表。如果你感到好奇，这是我们的`User`模型输出的结果:

```
CREATE TABLE "user" (
  "id" int NOT NULL AUTO_INCREMENT,
  "username" varchar(255) NOT NULL,
  "password" text NOT NULL,
  "email" varchar(255) NOT NULL,
  "first_name" varchar(255) DEFAULT NULL,
  "last_name" varchar(255) DEFAULT NULL,
  "bio" text,
  "avatar_url" text,
  "last_seen" datetime DEFAULT NULL,
  "created_at" datetime DEFAULT CURRENT_TIMESTAMP,
  "updated_at" datetime DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY ("id"),
  UNIQUE KEY "username" ("username"),
  UNIQUE KEY "email" ("email")
); 
```

Enter fullscreen mode Exit fullscreen mode

## 创建会话

会话是一个持久的数据库连接，它让我们可以轻松地添加、删除、更改甚至撤消更改。我们将使用刚刚创建的`User`模型通过数据库会话创建新用户。

会话是通过将它们绑定到 SQLAlchemy 引擎来创建的，我们在本系列的第 1 部分中讨论过。创建了引擎之后，我们只需要使用 SQLAlchemy 的`sessionmaker`来定义一个会话，并将其绑定到我们的引擎:

<figure>

```
"""Database engine & session creation."""
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine(
    'mysql+pymysql://user:password@host:3600/database',
    echo=True
)
Session = sessionmaker(bind=engine)
session = Session() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>database.py</figcaption>

</figure>

这就够了！我们将使用`session`和我们的`User`模型来创建一些用户。

## 用模型创建记录&会话

有了定义的模型和创建的会话，我们就可以完全用 Python 添加和修改数据了。SQLAlchemy 称之为**基于函数的查询构造**。让我们看看如何从我们的`User`类:
中创建一个新用户

<figure>

```
from models import User
from database import session

user = User(
    username="admin",
    password="Please don't set passwords like this",
    email="admin@example.com",
    first_name="Todd",
    last_name="Birchard",
    bio="I write tutorials on the internet.",
    avatar_url="https://example.com/avatar.jpg"
)

session.add(user) # Add the user session.commit() # Commit the change 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Adding a new user via a session</figcaption>

</figure>

创建了一个`User`的实例并保存为变量`new_user`，在我们的数据库中创建这个用户只需调用两次我们的会话:`add()`将项目排队等待创建，`commit()`保存更改。我们现在应该在数据库的**用户**表中看到一行！

使用`session`就像四种简单的方法一样简单:

*   `session.add()`:我们可以将一个数据模型的实例传递到`add()`中，以快速创建一个新记录，并将其添加到我们的数据库中。
*   `session.delete()`:和上面一样，`delete()`接受一个数据模型的实例。如果该记录存在于我们的数据库中，它将被暂存以便删除。
*   `session.commit()`:在会话中所做的更改直到显式提交后才会保存。
*   与 SQLAlchemy 引擎不同，会话是在显式关闭之前一直保持打开的连接。

删除记录的语法与创建记录的语法非常相似。有了`user`变量，删除我们创建的用户就像下面这样简单:

<figure>

```
...

session.delete(new_user)
session.commit() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Delete a record</figcaption>

</figure>

像变魔术一样，我们通过简单地将`new_user`传递到 delete 方法中，删除了与我们之前创建的记录相匹配的记录。不要只相信我的话:这个过程中最令人满意的部分是亲眼看着它发生！尝试使用您选择的 GUI 连接到您的数据库，并观察您运行的每一行代码创建和删除记录的情况。整洁！

## 上蜡，下蜡

创建和修改记录只是您使用 SQLAlchemy 的 ORM 之旅的开始。毕竟，我们仍然需要能够获取我们已经创建的记录！我们将在第 3 部分讨论通过数据模型获取行的乐趣，所以不要碰那个拨号盘！

无论如何，你可以从下面的 Github 中获取本教程的工作源代码。本系列每一章的源代码可以在这里找到:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[sqlalchemy-tutorial](https://github.com/hackersandslackers/sqlalchemy-tutorial)

### 🧪🔬使用 SQLAlchemy 连接、查询关系数据库并与之交互。

<article class="markdown-body entry-content container-lg" itemprop="text">

# SQLAlchemy 教程

[![Python](img/91eac0264bc3c2568bdd56361da1d1ae.png)](https://camo.githubusercontent.com/0b153563dd844ea8bbae9bf9d219c0d9930aa35e7d2ed0e07638a2caa3745902/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f507974686f6e2d76253545332e382d626c75652e7376673f6c6f676f3d707974686f6e266c6f6e6743616368653d74727565266c6f676f436f6c6f723d776869746526636f6c6f72423d356538316163267374796c653d666c61742d73717561726526636f6c6f72413d346335363661)[![SQLAlchemy](img/1723cabc44962dff2d2950b3c1d95405.png)](https://camo.githubusercontent.com/424e1a71c7e887f61c0f663ed11eab714a5dc2f4b93862cfd20eeab74517b58f/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f53514c416c6368656d792d76253545312e342e302d626c75652e7376673f6c6f6e6743616368653d74727565266c6f676f3d707974686f6e267374796c653d666c61742d737175617265266c6f676f436f6c6f723d776869746526636f6c6f72423d35653831616326636f6c6f72413d346335363661)[![PyMySQL](img/3feff7a175a540e1c892ce78f090ce77.png)](https://camo.githubusercontent.com/fdebcbd844cbae276b85296fea82e5730245a6f89610e81fa3b540a01ea62e19/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50794d7953514c2d76253545312e302e302d7265642e7376673f6c6f6e6743616368653d74727565267374796c653d666c61742d737175617265266c6f676f3d7363616c61266c6f676f436f6c6f723d776869746526636f6c6f72413d34633536366126636f6c6f72423d626636313661)[![GitHub Last Commit](img/ce450f99c39c42faeca9f81fb73c2c1d.png)](https://camo.githubusercontent.com/556bded58c9998ff95a5c6abd94d070c459b92b20b2a0977dc3a2f94af805fbe/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6173742d636f6d6d69742f676f6f676c652f736b69612e7376673f7374796c653d666c61742d73717561726526636f6c6f72413d34633536366126636f6c6f72423d613362653863266c6f676f3d476974487562)[![GitHub Issues](img/b51c92f47428b4e575d45f7be4c1e2e1.png)](https://github.com/hackersandslackers/sqlalchemy-tutorial/issues)[![GitHub Stars](img/8df379b96bf69df566c3607586fcb4f3.png)](https://github.com/hackersandslackers/sqlalchemy-tutorial/stargazers)[![GitHub Forks](img/d8103d43bdbf0b4b95b37afb2ff84af6.png)](https://github.com/hackersandslackers/sqlalchemy-tutorial/network)

[![SQLAlchemy Tutorial](img/18eb72b96df491820eb93712bdc663e3.png)](https://github.com/hackersandslackers/sqlalchemy-tutorial/blob/master/.github/sqlalchemy@2x.jpg?raw=true)

这个存储库包含关于 SQLAlchemy 的四部分教程系列的源代码:

1.  [使用 SQLAlchemy 简化 Python 中的数据库](https://hackersandslackers.com/python-database-management-sqlalchemy)
2.  用 SQLAlchemy 实现一个 ORM
3.  [SQLAlchemy 数据模型中的关系](https://hackersandslackers.com/sqlalchemy-data-models)
4.  [用 SQLAlchemy 构建数据库查询](https://hackersandslackers.com/database-queries-sqlalchemy-orm)

# 入门指南

分两步在本地进行设置:

### 环境变量

用您的值替换 **.env.example** 中的值，并将该文件重命名为**。环境**:

*   `SQLALCHEMY_DATABASE_URI`:SQL 数据库的连接 URI。
*   `SQLALCHEMY_DATABASE_PEM` *【可选】*:需要 SSL 连接的数据库的 PEM 密钥。

记住永远不要提交保存在。Github 的 env 文件。

### 装置

使用`make deploy`开始运行:

```
$ git clone https://github.com/hackersandslackers/sqlalchemy-tutorial.git
$ cd sqlalchemy-tutorial
$ make deploy
```

Enter fullscreen mode Exit fullscreen mode

* * *

**黑客和懒鬼**教程免费。如果你觉得这个教程有帮助，一个[的小额捐赠](https://www.buymeacoffee.com/hackersslackers)将会非常感谢，让我们继续做生意。所有的收益都流向了咖啡，所有的咖啡都流向了更多的内容。

</article>

[View on GitHub](https://github.com/hackersandslackers/sqlalchemy-tutorial)