# SQLAlchemy 中的身份验证哈希

> 原文：<https://dev.to/kaelscion/authentication-hashing-in-sqlalchemy-1bem>

> 更多精彩内容，请访问编码鸭博客:[ccstechme.com/coding-duck-blog](https://www.ccstechme.com/coding-duck-blog)

SQLAlchemy 是使用 Python 处理数据库的首选 ORM(对象关系模型)系统。ORM 基本上是 Python 和 SQL 之间的中间人，允许 Python 开发人员将数据库表创建为可以直接从 Python 操作的对象。

这个例子将使用 Flask 框架的 SQLAlchemy 风格，并假设您已经有了一个基本的 Flask 设置。创建一个新文件，命名为 models.py:

```
from flask_sqlalchemy import SQLAlchemy
from werkzeug.security import generate_password_hash, check_password_hash

db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    email = db.Column(db.String(120), index=True, unique=True)
    phone_number = db.Column(db.String(16), index=True)
    password_hash = db.Column(db.String(128)) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们导入 SQLAlchemy 类来处理数据库交互。在那里，我们定义了一个用户类(表),它有 id、email 和 phone_number 字段。通常，id 是稍后用于任何外键关系的 primary_key。Email 是一个字符串类型，最大长度为 120 个字符，phone_number 也是一个限制为 16 个字符的字符串(以防您计划处理国际号码)。最后一个字段是 password_hash，它是一个 128 个字符的散列。

SQLAlchemy 表的优点在于，它们也是对象，因此可以附加该表独有的其他属性和函数。在本例中，我们将添加两个函数，用于使用 werkzueg(Flask 附带的另一个优秀的库)创建密码散列，并根据该散列检查密码输入。

在用户表信息下面，让我们添加我们的功能。确保将这些函数保留在用户类中，以便只有用户数据库表可以访问生成和检查散列。同样，就 python 而言，SQLAlchemy 表实际上是*对象，所以它会像对待任何其他类一样对待该类中的所有属性和函数。SQLAlchemy 将对 SQL 进行必要的转换，并将其存储在相应的 DB 表中。* 

```
 class User(UserMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True)
    email = db.Column(db.String(120), index=True, unique=True)
    phone_number = db.Column(db.String(16), index=True)
    company_name = db.Column(db.String(128), index=True)
    first_name = db.Column(db.String(32), index=True)
    last_name = db.Column(db.String(32), index=True)
    password_hash = db.Column(db.String(128))

    #below our user model, we will create our hashing functions 
    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password) 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单！现在，我们可以到我们的 Python 终端，键入以下内容:

```
from app import db
from app.models import user

u = User(email="test@examplesite.com", phone_number="1111111111") 
```

Enter fullscreen mode Exit fullscreen mode

这个过程创建了一个新的用户对象，并将其赋给变量 u，实际上是在用户表中创建了一个新条目。新用户的邮箱是“[test@examplesite.com](mailto:test@examplesite.com)”，电话号码是“1111111111”。我猜他们在 T2 很难用那个号码联系到，所以如果你需要联系的话，我会试着发邮件😄。

但是他们没有密码散列！你说？不，他们没有。这是因为如果我们直接给数据库条目分配一个密码，然后创建一个散列，数据库将会在散列之前看到用户的密码并存储它。这将是一个很大的安全风险。最终，DB 对 Auth 信息了解得越少越好。如果我们可以帮助它，我们不想给它任何可以被恶意使用或被盗后有价值的信息。幸运的是，我们在用户类中创建了密码散列生成器:

```
u.set_password('P@ssw0rd')
db.session.add(u)
db.session.commit() 
```

Enter fullscreen mode Exit fullscreen mode

看到多简单了吗？本质上，set_password 是一个由任何用户对象继承的函数。这将获取函数调用的字符，并对它们进行 128 个字符的散列，而无需将密码存储在数据库中。然后，我们告诉 SQLAlchemy 将新用户添加到表中，并提交更改。从这里，我们可以加载这个用户对象并检查密码是否被保存:

```
u = User.query.filter_by(email="test@examplesite.com").first()
u.check_password('P@ssw0rd')
True
u.check_password('notherightpassword')
False 
```

Enter fullscreen mode Exit fullscreen mode

最后，后端开发涉及到数据库和系统架构的大量工作。要精通它，我们必须学会如何流利地对数据库说话。用原始 SQL 执行同样的任务会更快吗？几乎可以肯定。但这是开发商的选择，完全取决于项目的需要。数据库调用会以十、百、千或更大的数量到来吗？你想多快开发出这个产品并发布 MVP？你还有什么其他开销？还有比将数据库交互移植到原始 SQL 更重要的事情吗？考虑这些因素将有助于你决定 ORM 是否适合你。如果您决定是，现在您知道如何执行密码散列和基本的表设置！

非常感谢您的阅读！我们都在一起，尽我们所能建设最好的项目。我们一起建立的这个社区是一件美好的事情，因为它允许专家们自由地交流想法、理论和工作项目，证明总是可以建立更好的捕鼠器。很少有其他行业像软件开发一样回避对其突破的贪婪保护。真的很少看到这么多人如此兴奋地与这么多其他开发者分享他们出色的、有时可能有利可图的想法。更罕见的是，那些其他开发者更有兴趣帮助构建这个产品，而不是为自己窃取它。世界需要我们。需要你。所以，开始构建和快乐编码吧！