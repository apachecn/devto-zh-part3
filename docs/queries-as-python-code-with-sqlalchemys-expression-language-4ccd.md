# 使用 SQLAlchemy 表达式语言作为 Python 代码进行查询

> 原文：<https://dev.to/hackersandslackers/queries-as-python-code-with-sqlalchemys-expression-language-4ccd>

[![Queries as Python Code with SQLAlchemy's Expression Language](img/06279150528ba5b14f3590d9c1c27273.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wiMqO2t0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers-cdn.storage.googleapis.com/2019/07/sqlalchemy-queries.jpg)

到目前为止，我们的 SQLAlchemy 之旅已经涵盖了管理数据库连接和模型创建。然而，我们如何从数据库中提取我们想要的数据呢？

SQLAlchemy 的 ORM 查询 API 简化了我们编写数据库查询的方式。我们可以通过将检索数据的方法链接在一起，在 SQLAlchemy 会话上构造查询，而不是编写原始的 SQL 查询。我们将深入 SQLAlchemy 丰富的查询 API，了解查询数据的所有方法。

### 创建会话

我们在之前的[帖子](https://hackersandslackers.com/implement-sqlalchemy-orm/)中介绍了 SQLAlchemy 会话创建，并在之前的帖子[中解释了引擎的概念。如果你跳过了这些帖子，不要。以下为文案+面食礼遇:](https://hackersandslackers.com/python-database-management-sqlalchemy/) 

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

## 基本查询语法

让我们快速熟悉一下 SQLAlchemy 的查询 API 的基本结构。SQLAlchemy **session** 对象有一个`query()`方法，它接受我们之前定义的数据模型的 *raw* 类。下面是在`Customer`模型上运行的一个查询的开始；或者换句话说，对**客户** SQL 表的查询:

<figure>

```
"""Construct database queries from SQLAlchemy sessions."""
from .database import session
from .models import Customer

# Example structure of an ORM query records = session
    .query(Customer)
    .FUNCTION() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Select records from a SQLAlchemy session</figcaption>

</figure>

在我们的**会话**中调用`.query(Customer)`不是一个有效的查询，直到我们向链中添加一个方法。所有会话查询都以形成/预测查询结果的最终方法结束:

*   `all()`将返回*所有与我们的查询匹配的*记录作为对象列表。如果我们在上面的查询中使用 **all** ，我们将收到所有 Python 数据类型为`List[Customer]`的客户记录。
*   `first()`返回与查询匹配的第*条*记录，不管有多少条记录与查询匹配(什么构成“第一条”取决于您的表是如何排序的)。这相当于将`LIMIT 1`添加到 SQL 查询中。结果，返回的 Python 类型将是`Customer`。
*   `one()`对于我们正在执行的查询中最多应该存在一条记录的情况非常有用(想象一下通过主键进行查询)。在创建记录之前验证记录是否存在时，这种语法非常有用。
*   如果存在一个值，`scalar()`返回单个值，如果没有值，则 **None** ，如果返回多个记录，则引发异常。
*   `get([VALUE(S)])`根据模型的主键进行搜索，以返回主键等于所提供值的行。在需要搜索多个外键的情况下，`get()`也接受元组。最后，`get()`能否*也能*接受字典并返回列(字典键)与提供的值匹配的行。

为了创建更复杂的查询，我们将通过在原始查询上链接方法来添加查询:

<figure>

```
"""Construct database queries from SQLAlchemy sessions."""
from .database import session
from .models import Customer

# Example structure of an ORM query records = session
    .query(Customer)
    .METHOD_1()
    .METHOD_2()
    .FUNCTION() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Complex SELECT query</figcaption>

</figure>

### 查询结果

如果我们执行一个返回多条记录的查询，我们需要遍历它们来查看结果:

<figure>

```
"""Construct database queries from SQLAlchemy sessions."""
from .database import session
from .models import Customer

# Fetch all customer records records = session
    .query(Customer)
    .all()

# Loop over records for record in records:
    print(record) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Fetch all customer records and print the output</figcaption>

</figure>

默认情况下，SQLAlchemy ORM 将返回一个类的实例，这意味着上述操作将产生以下输出:

<figure>

```
<Customer model 1>
<Customer model 2>
<Customer model 3>
<Customer model 4>
<Customer model 5>
<Customer model 6>
<Customer model 7>
<Customer model 8>
<Customer model 9>
<Customer model 10> 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output of a session query</figcaption>

</figure>

如果你想得到字典，使用内置的`__dict__`方法:

<figure>

```
...

records = session
    .query(Customer)
    .all()

for record in records:
    pp.pprint(record. __dict__ ) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>View query results as dictionaries</figcaption>

</figure>

相反，这将为每一行返回字典对象:

<figure>

```
{ '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f98c8221748>,
    'email': 'kpaladini5i@senate.gov',
    'first_name': 'Kenna',
    'id': 199,
    'join_date': datetime.datetime(2019, 4, 19, 0, 0),
    'last_name': 'Paladini',
    'preferred_language': 'Bulgarian'}
{ '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x7f9918192d68>,
    'email': 'rlebrun5j@narod.ru',
    'first_name': 'Rriocard',
    'id': 200,
    'join_date': datetime.datetime(2015, 6, 8, 0, 0),
    'last_name': 'Le Brun',
    'preferred_language': 'Khmer'},
... 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output of a session query as dictionaries</figcaption>

</figure>

当然，您也可以创建自己的对象，只接收您想要/需要的列:

<figure>

```
...

# Fetch all customers records = session.query(Customer).all()

# Loop through records for record in records:
    recordObject = {
        'name': record.name,
        'position': record.position,
        'team_name': record.team.name,
        'team_city': record.team.city
    }
    print(recordObject) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Deserialize results of a query</figcaption>

</figure>

这输出了一些更清晰的东西:

<figure>

```
{ 'email': 'kpaladini5i@senate.gov',
    'first_name': 'Kenna',
    'join_date': datetime.datetime(2019, 4, 19, 0, 0),
    'last_name': 'Paladini',
    'preferred_language': 'Bulgarian'}
{ 'email': 'rlebrun5j@narod.ru',
    'first_name': 'Rriocard',
    'join_date': datetime.datetime(2015, 6, 8, 0, 0),
    'last_name': 'Le Brun',
    'preferred_language': 'Khmer'},
... 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output of query deserialization</figcaption>

</figure>

## 过滤结果

可能在查询中最常用的方法是`filter()`方法。`filter()`相当于 SQL **WHERE** 子句，只返回符合我们想要的条件的行:

<figure>

```
...

# Fetch records where `first_name` is `Carl` records = session
    .query(Customer)
    .filter(Customer)
    .first_name == 'Carl')
    .all() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Select all customers named Carl</figcaption>

</figure>

### filter_by()

我们可以使用`filter_by()`方法编写上面的查询，就像这样:

<figure>

```
...

# Fetch records where `first_name` is `Carl` records = session
    .query(Customer)
    .filter_by(first_name="Carl")
    .all() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Filter with `filter_by`</figcaption>

</figure>

与`filter()`不同，`filter_by()`接受关键字参数(注意这里的语法差异:`filter()`检查针对列对象的条件，而`filter_by()`查找匹配我们传递的参数的列)。`filter_by()`只能搜索精确的值，是简单过滤查询的一种简写方式。

### 喜欢()

我们可以做的不仅仅是过滤简单的条件。SQLAlchemy 有一个`like()`方法，其工作方式与 SQL 的`LIKE` :
相同

<figure>

```
...

# Fetch records where `first_name` begins with the letter `J` records = session
    .query(Customer)
    .filter(Customer.first_name.like('J%'))
    .all() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Select customer records where first names begin with "J"</figcaption>

</figure>

正如所料，这将给出客户名字以 **J** :
开头的所有行

<figure>

```
{ 'email': 'jpugsley9@netvibes.com',
    'first_name': 'Jarid',
    'join_date': datetime.datetime(2017, 10, 11, 0, 0),
    'last_name': 'Pugsley',
    'preferred_language': 'Burmese'}
{ 'email': 'jdymockek@is.gd',
    'first_name': 'Jeanna',
    'join_date': datetime.datetime(2017, 11, 13, 0, 0),
    'last_name': 'Dymocke',
    'preferred_language': 'Malayalam'}
... 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output</figcaption>

</figure>

### 高层查询方式

除了`filter()`之外，还有几个我们应该熟悉的基本方法。其中每个都对应于您可能熟悉的 SQL 关键字:

*   `limit([INTEGER])`:将行数限制为所提供的最大值。
*   `order_by([COLUMN])`:按提供的列对结果进行排序。
*   `offset([INTEGER])`:从第*行第 n* 行开始查询。

下一部分包括执行模型之间的连接查询，这要求我们首先定义模型之间的关系。目前事情有点混乱，因为我实际上要到下一篇文章的[才报道这个。抱歉，这里很乱，我正在努力！](https://hackersandslackers.com/sqlalchemy-data-models)

## 执行加入&工会

我们之前已经涉及到了一些连接，但是我们将把它提升一个档次。我们有两个正在使用的数据模型:一个用于**客户**，一个用于**订单**。每位顾客

<figure>

```
...
import pprint
from .models import Order, Customer

pp = pprint.PrettyPrinter(indent=4)

# Execute a SELECT query on JOINed tables records = session
    .query(Customer)
    .join(Order, Order.customer_id == Customer.id)
    .all()

# Loop through results for record in records:
    record_object = {
        'first_name': record.first_name,
        'last_name': record.last_name,
        'email': record.email,
        'preferred_language': record.preferred_language,
        'join_date': record.join_date,
        'orders': []
    }
    for order in record.order:
        order = {
            'order_price': order.price,
            'currency': order.currency,
            'purchase_date': order.purchase_date,
            'product': order.product
        }
        record_object['orders'].append(order)
        pp.pprint(record_object) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Join records from different tables and deserialize records</figcaption>

</figure>

我们使用`join()`方法执行连接。我们传递的第一个参数是我们将在“右边”加入的数据模型然后我们指定我们将加入的内容:我们的**订单**模型的 **customer_id** 列，以及我们的**客户**模型的 **id** 列。

我们的外部循环给我们每个客户，我们的内部循环将每个订单添加到适当的客户。查看一个示例记录:

<figure>

```
{ 'email': 'jtinline16@arizona.edu',
    'first_name': 'Jerry',
    'join_date': datetime.datetime(2016, 10, 27, 0, 0),
    'last_name': 'Tinline',
    'preferred_language': 'Icelandic',
    'orders': [{'currency': 'IDR',
                'order_price': 34.24,
                'product': 'Beer - Corona',
                'purchase_date': datetime.datetime(2019, 5, 5, 0, 0)},
               {'currency': 'GEL',
                'order_price': 25.75,
                'product': 'Creamers - 10%',
                'purchase_date': datetime.datetime(2019, 1, 27, 0, 0)}]} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output</figcaption>

</figure>

我们的朋友 Jerry 有两份订单:一份是一些 Coronas，另一份是 creamers。开始吧，杰瑞。

### 外部联接

除了简单的连接，我们可以使用相同的语法执行外部连接:

<figure>

```
...
from .models import ExampleModel1, ExampleModel2

# Execute an outer JOIN records = session
    .query(ExampleModel1)
    .outerjoin(ExampleModel2)
    .all() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Perform an outer join</figcaption>

</figure>

### 工会

我们也可以执行 UNION 和 UNION all:

<figure>

```
...
from .models import ExampleModel1, ExampleModel2

# Execute a UNION records = ExampleModel1.union(ExampleModel2) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Perform a union</figcaption>

</figure>

要执行 union all，只需用`union_all()`替换`union()`！

## 聚合函数和统计数据

与所有类似 SQL 的查询语言一样，我们也可以执行一些聚合统计。我们可以使用以下工具:

*   `count([COLUMN])`:统计一列中的记录数。
*   `count(distinct([COLUMN]))`:统计一列中*条不同的*条记录的数量。
*   `sum([COLUMN])`:将一列中的数值相加。

下面是我们如何执行一个对一列中的值进行计数的查询:

<figure>

```
...
from sqlalchemy import func

# Count number of records with a `first_name` value records = session
    .query(func.count(Customer.first_name))
    .all()   

for record in records:
    print(record) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Aggregate queries</figcaption>

</figure>

哪些输出:

<figure>

```
(200,) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output</figcaption>

</figure>

这个查询可以很容易地修改为只计算不同的值:

<figure>

```
...
from sqlalchemy import func
from sqlalchemy import distinct

# Count number of DISTINCT `first_name` values records = session
    .query(func.count(distinct(Customer.first_name)))
    .all()

for record in records:
    print(record) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Query to aggregate distinct results</figcaption>

</figure>

### 使用 Group_by()

当然，我们也可以对基于聚合的查询使用`group_by()`方法。`group_by()`的工作方式类似于我们对 SQL 和 Pandas 的期望:

<figure>

```
...

# Execute a `GROUP BY` aggregation query records = session
    .query(func.count(Customer.first_name))
    .group_by(Customer.first_name)
    .all() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>"Group by" aggregation</figcaption>

</figure>

## 突变

我们已经花了大量的时间来研究如何从数据库中提取数据，但是还没有谈到修改我们的数据！我们今天议程的最后一项是研究如何使用 SQLAlchemy ORM 添加、删除和更改记录。

### 插入行

我们添加数据的第一种方法是使用`add()`方法。`add()`期望一个类的实例(特别是数据模型)被传递，并将创建一个新的数据库行作为结果:

<figure>

```
from .database import session
from .models import Customer

# Inserting records via data models customer = Customer(
    first_name='Todd',
    last_name='Birchard',
    email='fake@example.com',
    preferred_language='English',
    join_date=datetime.now()
)
session.add(customer)
session.commit() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Insert records via ORM</figcaption>

</figure>

添加数据的另一种方法是使用`insert()`方法。与`add()`不同，`insert()`在 SQLAlchemy **表**对象上被调用，并且不依赖于接收数据模型。`insert()`不是 ORM 的一部分:

<figure>

```
...

# Inserting records via SQLAlchemy `Table` objects insert = [TABLE]
    .insert()
    .values(
        first_name='Todd',
        last_name='Jack Jones',
        email='fake@example.com',
        preferred_language='English',
        join_date=datetime.now()
    ) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Insert records</figcaption>

</figure>

### 更新

基于`insert()`的语法，我们可以加入`update()`方法来更改现有记录的值。我们用`where()`方法来指定哪些行应该被更新:

<figure>

```
...

# Updating records via SQLAlchemy `Table` objects result = [TABLE]
    .update()
    .where([TABLE].c.name == 'Todd')
    .values(email='newemail@example.com') 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Update records</figcaption>

</figure>

### 删除

在我们执行的任何查询中，我们可以添加`delete()`方法来删除该查询中包含的*所有行*(小心！).下面的代码删除了**名字**列包含值“卡尔”的所有记录:

<figure>

```
...

# Delete records where `first_name` is `Carl` result = session
    .query(Customer)
    .filter(Customer.first_name == 'Carl')
    .delete() 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Delete records</figcaption>

</figure>

`delete()`接受 **synchronize_session** 参数，该参数决定如何处理删除:

*   在提交会话之前，`False`不会执行删除。
*   `'fetch'`选择所有要删除的行，并删除匹配的行。
*   `'evaluate'`将评估当前会话中的对象，以确定应删除哪些行。

## 永不停止探索

为了简单起见，我们省略了很多内容。还有很多很酷的方法可以探索，比如`correlate()`方法。在 SQLAlchemy 中，你现在已经足够危险了，但是我鼓励任何人浏览一下[查询文档](https://docs.sqlalchemy.org/en/13/orm/query.html)，找到我们在这里没有详细讨论的酷东西。

我仍在努力将这篇文章的源代码放在下面的 Github repo 中。前几章的资料也可以在那里找到。同时，我为我的糟糕表现道歉:

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