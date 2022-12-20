# 共济会会员

> 原文：<https://dev.to/masonite/masonite-orm-orator-3bh0>

# 简介

几乎每个框架都带有某种形式的 ORM。ORM 是一个对象关系映射器，是一个价值 10 美元的单词和一个价值 1 美元的定义。用外行人的话来说，ORM 只是一种以“对象”相关的方式与数据库进行交互的方式。在 Python 的例子中，与“对象”相关的方式是使用类。

通常，一个表映射到一个类，因此我们可以与我们的类交互，而类又与我们的表交互。

我们称这些类为“模型”

Masonite 选择使用演说家表单作为它的表单。这个 ORM 可能和以前的 ORM 不一样。如果您来自任何其他 Python 框架，这对您来说都是一种新的学习体验。如果你来自一个像 Laravel 或 Ruby on Rails 这样的框架，那么这个 ORM 马上就会很有意义。

## 读之前

在您开始阅读之前，请注意，我们将在这里详细介绍 ORM 如何与 Masonite 框架一起使用，而不会详细介绍数据库迁移如何工作。这将需要它自己的文章，你可以在 Masonite 的[数据库迁移](https://docs.masoniteproject.com/orator-orm/database-migrations)文档页面上阅读更多相关内容。

一旦完成了数据库、迁移和表的设置，您就可以通过本文了解如何与之交互。

# 配置

如果您已经阅读了迁移文档，那么您可能已经有了配置设置，但是对于那些还没有阅读的人，我们可以在这里再次讨论。

Masonite 的配置非常简单。所有数据库配置文件都可以在您的`.env`文件中找到。在这个文件中，你会发现类似这样的东西:

```
DB_DRIVER=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=masonite
DB_USERNAME=root
DB_PASSWORD=root 
```

Enter fullscreen mode Exit fullscreen mode

继续设置您需要的任何配置设置。

# MySQL 和 Postgres

文件`.env`中的配置设置对于 MySQL 和 Postgres 来说已经足够好了，但是您还需要驱动程序来支持您的数据库连接。

如果你使用 MySQL，你有两个选择:

```
$ pip install PyMySQL 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$ pip install mysqlclient 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用 Postgres，那么你只有一个选择:

```
$ pip install psycopg2 
```

Enter fullscreen mode Exit fullscreen mode

# SQLite

如果你使用 SQLite，你还需要做更多的事情。你不需要任何驱动程序，但是你需要从`config/database.py`文件中删除一些设置。

打开该文件，应该有一个类似于:
的部分

```
DATABASES = {
    'default': {
        'driver': os.environ.get('DB_DRIVER'),
        'host': os.environ.get('DB_HOST'),
        'database': os.environ.get('DB_DATABASE'),
        'user': os.environ.get('DB_USERNAME'),
        'password': os.environ.get('DB_PASSWORD'),
        'prefix': ''
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于 SQLite，我们需要删除这里的`host`、`user`和`password`选项，使其成为

```
DATABASES = {
    'default': {
        'driver': os.environ.get('DB_DRIVER'),
        'database': os.environ.get('DB_DATABASE'),
        'prefix': ''
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 SQLite 不需要这些选项，如果找到它们，就会抛出异常。

# 用法

很好，如果您已经做到了这一步，那么您应该知道如何通过阅读迁移文档来进行迁移，应该知道如何配置 MySQL 或 Postgres。现在我们只需要知道如何访问数据库中的数据。

## 获取数据

有几种不同的方法可以从表中获取数据。出于本文的目的，我们将使用`User`模型来概括一切。

### 按键获取

通过使用需要 ID:
的`find()`方法，我们可以根据主键获取特定的行

```
def show(self):
    User.find(1) 
```

Enter fullscreen mode Exit fullscreen mode

这将返回主键为`1`的记录。

### 获取所有记录

很多时候你都想得到一个表的所有记录。你可以这样做:

```
def show(self):
    User.all() 
```

Enter fullscreen mode Exit fullscreen mode

这将返回表中所有结果的集合。

### 按值获取

我们还可以通过特定列的值来获取。如果使用原始 SQL，这将类似于一个`WHERE`关键字。也许不是巧合，这个方法叫做`where` :

```
def show(self):
    User.where('email', 'user@email.com').get() 
```

Enter fullscreen mode Exit fullscreen mode

这将返回结果的集合，即使是单个结果。每天不止一次，我通常会忘记在最后添加那个小的`get()`方法，所以确保你添加了那个方法，这样你就可以真正地获取结果。

### 获取第一条记录

我们还可以得到结果中的第一条记录，它将返回一个实际的模型而不是一个集合:

```
def show(self):
    User.where('email', 'user@email.com').first() 
```

Enter fullscreen mode Exit fullscreen mode

这将从集合中返回第一个结果。

### 聚合在哪里

您可以使用所谓的“聚合方法”，这只是一种收集一系列值的方法。

```
def show(self):
    User.where('id', '<' '2').get() 
```

Enter fullscreen mode Exit fullscreen mode

### 拔毛值

您可以从集合中“挑选”值。换句话说，您可以返回一个新的集合，其中只包含您选取的列中的值:

```
def show(self):
    User.where('id', '<' '2').get().pluck('email') 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个新的集合，其中只包含这些用户的电子邮件。

### 在哪里

您可以使用 this 方法获取其他值列表中的值。例如，我们可以获得我们提供的列表中的所有电子邮件记录:

```
def show(self):
    User.where_in('id', [1, 2]).get() 
```

Enter fullscreen mode Exit fullscreen mode

这将返回所有`id`为`1`或`2`的行。

这在使用上面的`pluck`方法时特别有用。我们可以从另一个模型中提取所有的`id`列。

我们可能想要调用所有有活跃作者的帖子，例如:

```
def show(self):
    Post.where_in(
        'id',
        User.where('active', '1').get().pluck('id')
    ) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码简单地获取所有活跃的用户，并提取他们的 ID，返回一个 ID 集合，然后我们可以用它传递给`where_in`方法，返回一个只包含活跃用户帖子的新结果。

### 不在哪里

我们可以做和上面一样的事情，但是通过使用不同的`where_not_in`方法:
让代码显示用户是不活动的

```
def show(self):
    Post.where_not_in(
        'id',
        User.where('active', '1').get().pluck('id')
    ) 
```

Enter fullscreen mode Exit fullscreen mode

这只是使用了`where_not_in`方法，而不是`where_in`方法。

# 更新信息

一旦我们获取了现有的信息，如果我们能够更新该 id，它可能会很有用。有几种方法可以做到这一点，我们将在这一部分进行探讨。

## 更新方法

我们可以使用 update 方法来更新我们获取的行:

```
def show(self):
    User.find(1).update(email='new@email.com') 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以传入一本字典:

```
def show(self):
    User.find(1).update({'user-email': 'new@email.com'}) 
```

Enter fullscreen mode Exit fullscreen mode

注意，当我们传入一个字典时，我们可以使用可能不是有效 Python 关键字参数的列名。这是两者之间唯一的区别。

我们还可以更新模型的属性，并调用`save()`方法:

```
def show(self):
    user = User.find(1)
    user.email = 'new@email.com'

    user.save() 
```

Enter fullscreen mode Exit fullscreen mode

### 更新多条记录

您可能希望一次更新几个记录，就像在某个操作之后将所有用户更新到活动状态:

```
def show(self):
    User.where('id', '>', 10).update(active=0) 
```

Enter fullscreen mode Exit fullscreen mode

这将对找到的所有记录进行更新。

### 更新时间戳

有时你只想更新一个记录的时间戳。例如，您可能想要获取一个用户，但是也要更新`modified_at`时间戳:

```
def show(self):
    user = User.find(1)
    user.touch() 
```

Enter fullscreen mode Exit fullscreen mode

# 创建新记录

创造新记录也非常简单。通过简单地创建一个新的模型实例，我们可以创建新的记录。

### 创建带有属性的新记录

```
def show(self):
    user = User()
    user.name = 'Joe'
    user.email = 'name@email.com'
    user.save() 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个新的记录。

### 用`create`方法创造新记录

我们可以用`create`的方法:

```
def show(self):
    user = User.create(name='Joe', email='name@email.com') 
```

Enter fullscreen mode Exit fullscreen mode

### 删除

我们还可以删除记录:

```
def show(self):
    User.find(1).delete()
    User.where('id', '>', 10).delete()
    User.destroy(1, 2, 3) 
```

Enter fullscreen mode Exit fullscreen mode

# 关系

与演说家的关系极其简单。在其他 Python 框架中，您可能已经在类属性或类特性中指定了外键关系。有了演说家，你就可以做一些相似但略有不同的事情。

## 有一个

假设我们有一个外键，将一个用户匹配到一个桌面。

我们可以像这样在模型中链接这种关系:

```
from orator.orm import has_one
from app.Desk import Desk

class User(Model):

    @has_one
    def desk(self):
        return Desk 
```

Enter fullscreen mode Exit fullscreen mode

演说家试图猜测外键是什么，这是基于以下逻辑:

```
# other_table_key, local_key @has_one('other_id', 'table_id') 
```

Enter fullscreen mode Exit fullscreen mode

并将 id 映射到表名。例如，这在技术上是默认值，并且与上面的型号代码完全相同:

```
from orator.orm import has_one
from app.Desk import Desk

class User(Model):

    @has_one('desk_id', 'user_id')
    def desk(self):
        return Desk 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的行为与上面的代码相同。您可能需要将它改为如下形式:

```
from orator.orm import has_one
from app.Desk import Desk

class User(Model):

    @has_one('id', 'user_desk')
    def desk(self):
        return Desk 
```

Enter fullscreen mode Exit fullscreen mode

这将把`user_desk`的本地列映射到`desk`表中`id`的外部列。

## 动态属性

我们可以使用“动态属性”来使用这种关系。这仅仅意味着从逻辑上来说，你可能认为你应该使用`desk()`方法作为一个方法，但是你却像调用一个属性一样调用它:

```
def show(self):
    User.find(1).desk.computer 
```

Enter fullscreen mode Exit fullscreen mode

注意，这里的 desk 不是像属性一样简单地被访问。

## 属于

属于关系定义了`has_one`关系的逆关系。*因为是逆关系，我们只需要翻转外键*T3】

```
from orator.orm import belongs_to
from app.Desk import Desk

class User(Model):

    @belongs_to('user_desk', 'id')
    def desk(self):
        return Desk 
```

Enter fullscreen mode Exit fullscreen mode

所以现在地图应该是:

```
# local_key, other_table_key @belongs_to('table_id', 'other_id') 
```

Enter fullscreen mode Exit fullscreen mode

## 有许多

你也可以用同样的方法指定一个有许多关系的。任何给定的用户可能有许多任务。

```
from orator.orm import has_many
from app.Task import Task

class User(Model):

    @has_many('user_desk', 'id')
    def desk(self):
        return Task 
```

Enter fullscreen mode Exit fullscreen mode

所以现在地图应该是:

```
# other_table_key, local_key @has_many('other_id', 'table_id') 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要更高级的关系或更多的解释，你可以在演讲者文档页阅读更多关于模型关系的内容。

# 循环依赖

循环依赖在 Python 中并不好，因为它使两个类相互依赖，导入一个类可能无法及时导入另一个类，并且会抛出无法找到所需类的异常。

如果两个模型相互依赖，那么在与模型进行交互时，你可能会遇到一个大问题。如果您在每个模型的顶部导入所有的模型，过一会儿您就会遇到这些循环依赖问题。为了避免这一点，在建立关系时，你应该总是懒的导入:

```
from orator.orm import has_many

class User(Model):

    @has_many('user_desk', 'id')
    def desk(self):
        from app.Task import Task
        return Task 
```

Enter fullscreen mode Exit fullscreen mode

这将减少出现循环导入问题的可能性。