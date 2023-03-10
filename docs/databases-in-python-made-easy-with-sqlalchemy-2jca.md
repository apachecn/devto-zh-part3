# 用 SQLAlchemy 简化 Python 中的数据库

> 原文：<https://dev.to/hackersandslackers/databases-in-python-made-easy-with-sqlalchemy-2jca>

[![Databases in Python Made Easy with SQLAlchemy](img/9855ae4da3e7e3d6a81f0db55c26ec58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8GOCpMzf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hackersandslackers.com/2020/02/sqlalchemy-intro-1.jpg)

当我们想到软件与数据库直接交互时，许多人会想到类似的管理连接、显式提交查询和类似的令人厌倦的样板文件的模式。像 **[PyMySQL](https://hackersandslackers.com/python-mysql-pymysql/)** 和 **[Psycopg2](https://hackersandslackers.com/psycopg2-postgres-python/)** 这样的 Python 库很好地适应了这种范式，维护了我们已经接受的现状。SQLAlchemy 已经成为 Python 最具标志性的库之一，它将平凡的数据库抽象为软件。

它也是我不幸浏览过的最糟糕的 Python 库文档的所在地。

SQLAlchemy 的 API 提供了一种客观上更好、更简单、更快速的处理关系数据库的方法。对于那些试图理解伪装成 SQLAlchemy 文档的深奥术语的人来说，这是没有用的，SQLAlchemy 文档打了你一巴掌，说，*“我他妈的不在乎你。”*

我在这里说，我不在乎你，我只想用 SQLAlchemy 教程的形式来表达我的感受。

### SQL 还是 ORM？

SQLAlchemy 有两个用途:使 SQL 交互更容易，以及作为一个成熟的 ORM。即使您对实现 ORM 不感兴趣(或者不知道 ORM 是什么)，SQLAlchemy 作为简化 SQL 数据库连接和执行原始查询的工具也是非常宝贵的。我认为这是我写的原创声明，但事实证明 SQLAlchemy 是这样描述自己的:

> SQLAlchemy 由两个不同的组件组成，称为**核心**和 **ORM** 。核心本身是一个全功能的 SQL 抽象工具包，提供了一个平滑的抽象层，覆盖了各种 DBAPI 实现和行为，以及一个 SQL 表达式语言，允许通过生成 Python 表达式来表达 SQL 语言。

## 连接到数据库

SQLAlchemy 为我们提供了一些管理数据库连接的选项，但是它们都是从一个**引擎**的概念开始的。“引擎”是代表数据库的 Python 对象。引擎是通过传入目标数据库的连接信息预先创建的。

一旦创建了引擎，我们就可以通过与引擎对象进行交互，随时快速地处理我们的数据库。将 SQL 语句直接传递给引擎让 SQLAlchemy 处理打开新连接、执行和立即关闭连接。在复杂的 web 应用程序支持大量数据传输的情况下，最好通过一个**会话**保持持续的数据库连接。我们将在下一篇文章中讨论这个问题。

创建引擎的语法非常简单。`create_engine()`需要一个位置参数，它是表示连接到数据库的连接信息的字符串:

<figure>

```
"""Create database connection."""
from sqlalchemy import create_engine

engine = create_engine(
    "mysql+pymysql://user:password@host:3600/database",
) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>SQLAlchemy database engine</figcaption>

</figure>

传入`create_engine()`的字符串是一个连接 URI。假设你有一个数据库，我们将一起找出你的 URI 应该是什么样子。

### 连接 URIs 数据库

将 URI 分解成不同的部分看起来是这样的:

<figure>

```
[DB_TYPE]+[DB_CONNECTOR]://[USERNAME]:[PASSWORD]@[HOST]:[PORT]/[DB_NAME] 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Database connection URI structure</figcaption>

</figure>

*   **【DB _ TYPE】**:指定我们正在连接的数据库的种类(方言)。SQLAlchemy 可以与所有主流的关系数据库接口。根据您正在连接的数据库，用匹配的方言替换`[DB_TYPE]`:
    *   **MySQL** : `mysql`
    *   **PostgreSQL** : `postgresql`
    *   **SQLite** : `sqlite`
    *   **神谕**(唉):`oracle`
    *   **微软 SQL** (稍微不那么恼怒的“啊”):`mssql`
*   **【DB _ CONNECTOR】**:为了管理您的数据库连接，SQLAlchemy 利用您选择使用的任何 Python 数据库连接库。如果你不确定这意味着什么，这里是我推荐的每个方言的库:
    *   **MySQL** : [pymysql](https://pypi.org/project/PyMySQL/) ， [mysqldb](https://pypi.org/project/MySQL-python/)
    *   **PostgreSQL**:[psycopg 2](https://pypi.org/project/psycopg2/)， [pg8000](https://pypi.org/project/pg8000/)
    *   **SQLite** :(不需要)
    *   **甲骨文**:[CX _ 甲骨文](https://pypi.org/project/cx-Oracle/)
    *   **微软 SQL** : [pymssql](https://pypi.org/project/pymssql/) ， [pyodbc](https://pypi.org/project/pyodbc/) <！- kg-card-end: markdown - >

后面的变量应该看起来都很熟悉；这些是指您的目标数据库的 URL、数据库用户、该用户的密码等。

这样一来，我们可以将 SQLAlchemy 与您的连接器一起安装:

<figure>

```
$ pip install sqlalchemy pymysql 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Install SQLAlchemy & connector</figcaption>

</figure>

### 附加发动机配置

除了连接 URI 之外，还可以配置引擎来满足特定数据库设置的特定需求(或偏好)。我个人要求通过 SSL 建立连接；这需要提供一个 PEM 密钥来保证额外的安全性，我们可以在创建引擎时通过关键字参数`connect_args`传递这个密钥:

<figure>

```
"""Create database connection."""
from sqlalchemy import create_engine
from config import SQLALCHEMY_DATABASE_PEM

engine = create_engine(
    "mysql+pymysql://user:password@host:3600/database",
    connect_args={"ssl": {"key": SQLALCHEMY_DATABASE_PEM}},
    echo=True,
) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>SQLAlchemy database engine</figcaption>

</figure>

如果您是好奇型的，您也可以将`echo=True`传递到您的引擎，它将在发生时将所有正在执行的 SQL 语句打印到您的控制台(包括连接、表创建等)。).这对了解新来者的情况很有帮助，但很快就会变得令人讨厌和垃圾。

## 执行查询

我们可以使用`engine.execute("SELECT * FROM mytable")`直接在引擎对象上运行特定查询。当我们在引擎上调用`execute()`时，SQLAlchemy 处理:

*   打开与我们数据库的连接。
*   在我们的数据库上执行原始 SQL 并返回结果。
*   立即关闭用于运行该查询的数据库连接，以避免连接挂起。

这种与数据库交互的方式被称为**显式无连接执行 _** 。这种类型的操作是“显式”的，因为查询在执行时会自动提交*(与传统的 Python 库形成对比，传统的 Python 库遵守 [PEP-249](https://www.python.org/dev/peps/pep-0249/#id3) ，除非后跟`commit()`，否则不会运行查询)。*

 *当我们想要按需运行特定查询时(例如:几乎所有不是面向用户的应用程序的情况)，这是非常好的。我们可以获取或改变我们想要的数据，而不用担心额外的东西。

### 选择数据

我为这个例子建立了一个虚拟数据的数据库。为了演示使用 SQLAlchemy 有多简单，我将从一个名为 **nyc_jobs** :
的表中获取一些数据

<figure>

```
...
from sqlalchemy import text

result = engine.execute(
    text(
        "SELECT job_id, agency, business_title, \
 salary_range_from, salary_range_to \
 FROM nyc_jobs ORDER BY RAND();"
    )
)
print(result) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Execute a SELECT query</figcaption>

</figure>

我已经向`engine.execute()`传递了一个简单的 SQL 查询，它从我的 **nyc_jobs** 表中选择了几列(以随机顺序返回)。让我们检查一下它的输出。

<figure>

```
<sqlalchemy.engine.result.Result object at 0x10c59bdc0> 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Queries return a `Result` object</figcaption>

</figure>

天哪，那是什么？！什么是`Result`？这一切好得令人难以置信吗？！

从 SQLAlchemy 1.4.X 开始，对象`ResultProxy`被重命名为`Result`。检查您的 SQLAlchemy 版本，以了解在获取结果时除了哪个对象。

### 解析查询结果

`Result`(是一个有用的数据结构，它总结了我们的查询结果并包装了结果本身。`Result`对象通过让我们访问属性，比如`rowcount` :
，让我们很容易从高层次上了解我们的查询是如何成功的

<figure>

```
...
from sqlalchemy import text

result = engine.execute(
    text(
        "SELECT job_id, agency, business_title, \
 salary_range_from, salary_range_to \
 FROM nyc_jobs ORDER BY RAND();"
    )
)
print(f"Selected {result.rowcount} rows.") 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Number of rows returned by query</figcaption>

</figure>

这给了我们...

<figure>

```
Selected 3123 rows. 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output of `results.rowcount`</figcaption>

</figure>

...别闹了。让我们通过在`Result` :
上调用`fetchall()`来看看我们的数据是什么样子的

<figure>

```
...
from sqlalchemy import text

result = engine.execute(
    text(
        "SELECT job_id, agency, business_title, \
 salary_range_from, salary_range_to \
 FROM nyc_jobs ORDER BY RAND() LIMIT 10;"
    )
)
print(f"Selected {result.rowcount} rows.")
for row in result.fetchall():
    print(row) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Print rows fetched from query</figcaption>

</figure>

现在我们来谈谈:

<figure>

```
Selected 10 rows.
(399274, 'NYC HOUSING AUTHORITY', 'Vice President Portfolio Planning, Project Development and Asset Mgt', 86346, 217244)
(399276, 'BOROUGH PRESIDENT-QUEENS', 'Director of Graphic Design', 52524, 81535)
(399276, 'BOROUGH PRESIDENT-QUEENS', 'Director of Graphic Design', 52524, 81535)
(399300, 'NYC HOUSING AUTHORITY', 'VICE PRESIDENT TRANSACTIONS', 86346, 217244)
(399300, 'NYC HOUSING AUTHORITY', 'VICE PRESIDENT TRANSACTIONS', 86346, 217244)
(399349, 'CONSUMER AFFAIRS', 'Deputy Director of Special Applications and Operations', 67324, 90686)
(399349, 'CONSUMER AFFAIRS', 'Deputy Director of Special Applications and Operations', 67324, 90686)
(399355, 'DEPT OF INFO TECH & TELECOMM', 'Assistant General Counsel', 66326, 99394)
(399357, 'DEPARTMENT OF BUILDINGS', 'Deputy Commissioner, Development and Technical Affairs', 86346, 212960)
... 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Query results</figcaption>

</figure>

嘿，那些看起来像包含工作列表的行！乍一看，这些“行”看起来是元组列表，但是 SQLAlchemy 还有一个锦囊妙计:

<figure>

```
...

for row in results.fetchall():
    print(row['business_title']) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Get column value per row</figcaption>

</figure>

哇，我刚刚是不是试着用`row['business_title']`从一个元组中获取一个键？我疯了吗？事实证明，我们的“行”根本不是元组；它们是被称为`RowProxy` :
的数据结构

<figure>

```
Selected 10 rows.
COMPUTER SPECIALIST (SOFTWARE)
Resident Engineer
Resident Engineer
Supervising Demolition Inspector
Supervising Demolition Inspector
Senior Inspector
Senior Inspector
Director for Pre-Development Planning
Director for Pre-Development Planning
... 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output of `row['business_title']`</figcaption>

</figure>

**business_title** 是我们表格的列名之一。通过循环`row['business_title']`，我们可以看到数据库中的每个职位名称；就像遍历字典列表一样。事实上，让我们这样做:让我们将查询结果作为字典列表返回:

<figure>

```
...

rows = [dict(row) for row in results.fetchall()]
print(rows) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Parse rows as list of dictionaries</figcaption>

</figure>

现在你有了一种格式，无论你想实现什么，它都一定会满足你的需求:

```
[  ...  {  "agency":  "DEPT OF INFO TECH & TELECOMM",  "business_title":  "Information Security Analyst (Data Protection)",  "job_id":  390003,  "salary_range_from":  75000,  "salary_range_to":  100000  },  {  "agency":  "HRA/DEPT OF SOCIAL SERVICES",  "business_title":  "JAVA DEVELOPER",  "job_id":  384199,  "salary_range_from":  75000,  "salary_range_to":  180000  },  {  "agency":  "DEPT OF HEALTH/MENTAL HYGIENE",  "business_title":  "Early Childhood Education Consultant, Bureau of Childcare",  "job_id":  386535,  "salary_range_from":  64507,  "salary_range_to":  80118  },  {  "agency":  "DEPT OF ENVIRONMENT PROTECTION",  "business_title":  "Air Pollution Inspector",  "job_id":  401509,  "salary_range_from":  43014,  "salary_range_to":  43014  },  {  "agency":  "DEPT OF DESIGN & CONSTRUCTION",  "business_title":  "Safety Code Compliance Auditor",  "job_id":  385796,  "salary_range_from":  54409,  "salary_range_to":  73124  },  {  "agency":  "DEPT OF ENVIRONMENT PROTECTION",  "business_title":  "Procurement Specialist",  "job_id":  378936,  "salary_range_from":  48399,  "salary_range_to":  78211  },  {  "agency":  "DEPT OF DESIGN & CONSTRUCTION",  "business_title":  "College Aide",  "job_id":  401628,  "salary_range_from":  16,  "salary_range_to":  20  },  {  "agency":  "DEPT OF ENVIRONMENT PROTECTION",  "business_title":  "Engineering Technician I",  "job_id":  348589,  "salary_range_from":  36239,  "salary_range_to":  41675  },  {  "agency":  "DEPT OF DESIGN & CONSTRUCTION",  "business_title":  "Executive Assistant",  "job_id":  395553,  "salary_range_from":  38851,  "salary_range_to":  60990  }  ...  ] 
```

Enter fullscreen mode Exit fullscreen mode

### 更新行

我们可以像运行选择查询一样运行更新查询，但要注意的是，有一个*坏的*更新查询。让我们确保避免这些。

我从表中随机选择一行进行更新。我要把第 **229837** 行改成 JSON 形式的样子:

<figure>

```
[  {  "job_id":  229837,  "agency":  "DEPT OF INFO TECH & TELECOMM",  "business_title":  "Senior Quality Oversight Analyst",  "job_category":  "Information Technology & Telecommunications",  "salary_range_from":  58675,  "salary_range_to":  125000,  "salary_frequency":  "Annual",  "work_location":  "75 Park Place New York Ny",  "division":  "General Counsel",  "created_at":  "2016-02-17T00:00:00.000",  "updated_at":  "2016-02-17T00:00:00.000"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>`SELECT * FROM nyc_jobs WHERE job_id = 229837;`</figcaption>

</figure>

让我们编写一个潜在危险的 SQL 查询，它引入了一些有问题的字符:

<figure>

```
UPDATE
    nyc_jobs
SET
    business_title = 'Senior QA Scapegoat 🏆',
    job_category = 'Info <>!#%%Technology%%#^&%* & Telecom' 
WHERE
    job_id = 229837; 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>UPDATE query containing quotes and emojis</figcaption>

</figure>

在值包含肯定会破坏操作的操作符的情况下尝试更新是一个坏主意。让我们看看会发生什么:

<figure>

```
result = engine.execute(
    text(
        "UPDATE nyc_jobs SET business_title = 'Senior QA Scapegoat 🏆', \
 job_category = 'Information? <>!#%%Technology!%%#^&%* & Telecom' \
 WHERE job_id = 229837;"
    )
)
print(result.rowcount) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Executing a problematic UPDATE query</figcaption>

</figure>

运行这个会让*所有的事情* :

<figure>

```
2021-01-09 15:44:14,122 INFO sqlalchemy.engine.base.Engine UPDATE nyc_jobs SET business_title = 'Senior QA Scapegoat 🏆', job_category = 'Information <>!#%%Technology%%#^&%* & Telecommunications' WHERE job_id = 229837;
2021-01-09 15:44:14,123 INFO sqlalchemy.engine.base.Engine {}
2021-01-09 15:44:14,123 INFO sqlalchemy.engine.base.Engine ROLLBACK
Traceback (most recent call last):
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/main.py", line 4, in <module>
    init_script()
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/sqlalchemy_tutorial/ __init__.py", line 17, in init_script
    rows_updated = update_job_listing(db)
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/sqlalchemy_tutorial/queries.py", line 18, in update_job_listing
    result = db.execute(
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/sqlalchemy/engine/base.py", line 2235, in execute
    return connection.execute(statement, *multiparams, **params)
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/sqlalchemy/engine/base.py", line 1003, in execute
    return self._execute_text(object_, multiparams, params)
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/sqlalchemy/engine/base.py", line 1172, in _execute_text
    ret = self._execute_context(
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/sqlalchemy/engine/base.py", line 1316, in _execute_context
    self._handle_dbapi_exception(
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/sqlalchemy/engine/base.py", line 1514, in _handle_dbapi_exception
    util.raise_(exc_info[1], with_traceback=exc_info[2])
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/sqlalchemy/util/compat.py", line 182, in raise_
    raise exception
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/sqlalchemy/engine/base.py", line 1276, in _execute_context
    self.dialect.do_execute(
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/sqlalchemy/engine/default.py", line 609, in do_execute
    cursor.execute(statement, parameters)
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/pymysql/cursors.py", line 146, in execute
    query = self.mogrify(query, args)
  File "/Users/toddbirchard/projects/sqlalchemy-tutorial/.venv/lib/python3.8/site-packages/pymysql/cursors.py", line 125, in mogrify
    query = query % self._escape_args(args, conn)
TypeError: * wants int 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Stack trace for bad SQL UPDATE query</figcaption>

</figure>

对我们来说幸运的是，SQLAlchemy 有一个名为`text()`的方法，它可以对类似查询中的危险字符进行转义。 ***总是*** 用这种方法包装你的查询:

<figure>

```
...
from sqlalchemy import text

result = db.execute(
    text(
        "UPDATE nyc_jobs SET business_title = 'Senior QA Scapegoat 🏆', \
 job_category = 'Information <>!#%%Technology%%#^&%* & Telecom' \
 WHERE job_id = 229837;"
    )
)
print(result.rowcount) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Wrap query in `text()`</figcaption>

</figure>

我们现在没有收到任何错误，并且返回了一个计数`1`,表示有一行被更新。

## 还有这么多

除非您是受虐狂，否则运行查询的 SQLAlchemy 工作流客观上比使用普通 DB 连接器时要处理的样板文件要好。我无法量化我个人从这个简化的工作流程中节省的时间，这使得下面的断言看起来很奇怪:从 SQLAlchemy 中还可以获得更多。

反正一步一个脚印。我在下面的 Github 上做了一个工作演示，包含了我们在本教程中涉及的所有源代码。下次加入我们吧！

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

[View on GitHub](https://github.com/hackersandslackers/sqlalchemy-tutorial)*