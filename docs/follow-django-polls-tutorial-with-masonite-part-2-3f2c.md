# 遵循 Django 民意测验教程和 Masonite 第 2-1/2 部分

> 原文：<https://dev.to/kentaro0919/follow-django-polls-tutorial-with-masonite-part-2-3f2c>

# [【Github】](https://github.com/kentaro0919/masonite_polls)

# 数据库设置

[mason ite 中的数据库迁移](https://docs.masoniteproject.com/orator-orm/database-migrations)非常好。
你不必把你的信息保存在你的配置文件中。你把信息储存在”。env”文件，Masonite 将处理该文件。Masonite 支持 MySQL、Postgres 和 SQLite。

```
#.env 
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=masonite
DB_USERNAME=root
DB_PASSWORD=root
DB_LOG=True 
```

为了遵循 Django 教程，我们使用 SQLite。

```
#.env 
DB_CONNECTION=sqlite
DB_DATABASE=masonite.db
DB_LOG=True 
```

# 创建模型

在姜戈。

```
#polls/models.py from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0) 
```

## 问题

在 Masonite 中，模型和迁移是分开的。
要创建新的迁移，您可以运行。

```
$ craft model Question -m
Model Created Successfully!
Created migration: 2019_03_20_130251_create_questions_table.py 
```

这将创建一个名为问题的模型，并调用[演说家 make:migration](https://orator-orm.com/docs/0.9/migrations.html#creating-migrations)

```
app
└─── Question.py

databases/migrations
├── 2018_01_09_043202_create_users_table.py # This exist at craft install
├── 2019_03_20_130251_create_questions_table.py
└── __init__.py 
```

```
#/databases/migrations/2019_03_20_130251_create_questions_table.py from orator.migrations import Migration

class CreateQuestionsTable(Migration):

    def up(self):
        """
        Run the migrations.
        """
        with self.schema.create('questions') as table:
            table.increments('id')
            table.timestamps()

    def down(self):
        """
        Revert the migrations.
        """
        self.schema.drop('questions') 
```

将此编辑为

```
from orator.migrations import Migration

class CreateQuestionsTable(Migration):

    def up(self):
        """
        Run the migrations.
        """
        with self.schema.create('questions') as table:
            table.increments('id')

            table.char('question_text', 200)
            table.datetime('pub_date')

            table.timestamps()

    def down(self):
        """
        Revert the migrations.
        """
        self.schema.drop('questions') 
```

和来自
的模型

```
# app/Question.py 
"""Question Model"""

from config.database import Model

class Question(Model):
    """Question Model"""
    pass 
```

至

```
"""Question Model"""

from config.database import Model

class Question(Model):
    """Question Model"""

    __fillable__ = ['question_text', 'pub_date']

    __dates__ = ['pub_date'] 
```

## 选择

以及选择。

```
$ craft model Choice  -m
Model Created Successfully!
Created migration: 2019_03_20_131816_create_choices_table.py 
```

```
app
├── Choice.py
└─── Question.py

databases/migrations
├── 2018_01_09_043202_create_users_table.py
├── 2019_03_20_130251_create_questions_table.py
├── 2019_03_20_131816_create_choices_table.py
└── __init__.py 
```

```
# /databases/migrations/2019_03_20_131816_create_choices_table.py from orator.migrations import Migration

class CreateChoicesTable(Migration):

    def up(self):
        """
        Run the migrations.
        """
        with self.schema.create('choices') as table:
            table.increments('id')
            table.timestamps()

    def down(self):
        """
        Revert the migrations.
        """
        self.schema.drop('choices') 
```

将此编辑为

```
# /databases/migrations/2019_03_20_131816_create_choices_table.py from orator.migrations import Migration

class CreateChoicesTable(Migration):

    def up(self):
        """
        Run the migrations.
        """
        with self.schema.create('choices') as table:
            table.increments('id')

            table.char('choice_text', 200)
            table.integer('votes').default(0)

            table.integer('question_id').unsigned()
            table.foreign('question_id').references('id').on('questions').on_delete('cascade')

            table.timestamps()

    def down(self):
        """
        Revert the migrations.
        """
        self.schema.drop('choices') 
```

```
#/app/Choice.py """Choice Model"""

from config.database import Model

class Choice(Model):
    """Choice Model"""
    pass 
```

至

```
#/app/Choice.py """Choice Model"""

from config.database import Model

class Choice(Model):
    """Choice Model"""

    __fillable__ = ['choice_text', 'pub_date', 'question_id'] 
```

该命令将对数据库进行更改。

```
$ craft migrate

#It took 1.94ms to execute the query ("SELECT * FROM sqlite_master WHERE type = #'table' AND name = ?", ['migrations'])
#It took 3.5ms to execute the query ('CREATE TABLE "migrations" ("migration" VARCHAR #NOT NULL, "batch" INTEGER NOT NULL)', None)
#It took 0.13ms to execute the query ('SELECT "migration" FROM "migrations"', [])
#It took 0.17ms to execute the query ('SELECT MAX("batch") AS aggregate FROM #"migrations"', [])
#It took 1.81ms to execute the query ('CREATE TABLE "users" ("id" INTEGER NOT NULL #PRIMARY KEY AUTOINCREMENT, "name" VARCHAR NOT NULL, "email" VARCHAR NOT NULL, #"password" VARCHAR NOT NULL, "remember_token" VARCHAR NULL, "verified_at" DATETIME #NULL, "created_at" DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL, "updated_at" #DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL)', None)
#It took 1.71ms to execute the query ('CREATE UNIQUE INDEX users_email_unique ON #"users" ("email")', None)
#It took 1.33ms to execute the query ('INSERT INTO "migrations" ("migration", #"batch") VALUES (?, ?)', ['2018_01_09_043202_create_users_table', 1])
#It took 1.93ms to execute the query ('CREATE TABLE "questions" ("id" INTEGER NOT #NULL PRIMARY KEY AUTOINCREMENT, "question_text" VARCHAR NOT NULL, "pub_date" #DATETIME NOT NULL, "created_at" DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL, #"updated_at" DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL)', None)
#It took 1.49ms to execute the query ('INSERT INTO "migrations" ("migration", #"batch") VALUES (?, ?)', ['2019_03_20_130251_create_questions_table', 1])
#It took 1.77ms to execute the query ('CREATE TABLE "choices" ("id" INTEGER NOT NULL #PRIMARY KEY AUTOINCREMENT, "choice_text" VARCHAR NOT NULL, "votes" INTEGER NOT NULL #DEFAULT \'0\', "question_id" INTEGER NOT NULL, "created_at" DATETIME DEFAULT #CURRENT_TIMESTAMP NOT NULL, "updated_at" DATETIME DEFAULT CURRENT_TIMESTAMP NOT #NULL, FOREIGN KEY("question_id") REFERENCES "questions"("id") ON DELETE cascade)', #None)
#It took 1.47ms to execute the query ('INSERT INTO "migrations" ("migration", #"batch") VALUES (?, ?)', ['2019_03_20_131816_create_choices_table', 1])
#Migration table created successfully
#[OK] Migrated 2018_01_09_043202_create_users_table
#[OK] Migrated 2019_03_20_130251_create_questions_table
#[OK] Migrated 2019_03_20_131816_create_choices_table 
```

craft 可以基于表定义
生成模型 docstring

```
$ craft model:docstring questions 
```

```
"""Model Definition (generated with love by Masonite)

id: integer default: None
question_text: string(255) default: None
pub_date: datetime default: None
created_at: datetime default: CURRENT_TIMESTAMP
updated_at: datetime default: CURRENT_TIMESTAMP
""" 
```

```
$ craft model:docstring choices 
```

```
"""Model Definition (generated with love by Masonite)

id: integer default: None
choice_text: string(255) default: None
votes: integer default: 0
question_id: integer default: None
created_at: datetime default: CURRENT_TIMESTAMP
updated_at: datetime default: CURRENT_TIMESTAMP
""" 
```

# 玩弄 API

在丹戈。

```
$ python manage.py shell

>>> from polls.models import Choice, Question  # Import the model classes we just wrote.

# No questions are in the system yet.
>>> Question.objects.all()
<QuerySet []>

# Create a new Question.
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID.
>>> q.id
1

# Access model field values via Python attributes.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]> 
```

在 Masonite 中，我们可以用 *craft tinker* 命令加载我们的应用程序。

```
$craft tinker
#Masonite Python 3.7.2 Console
#This interactive console has the following things imported:
#    container as 'app'

#Type `exit()` to exit.

>>> from app.Question import Question

>>> Question.all()
It took 1.37ms to execute the query ('SELECT * FROM "questions"', [])
#<orator.orm.collection.Collection object at 0x106967e80>

>>> import datetime

>>> q = Question.create(question_text="What's new?", pub_date=datetime.datetime.now())
#It took 350.62ms to execute the query ('INSERT INTO "questions" ("pub_date", #"question_text") VALUES (?, ?)', [datetime.datetime(2019, 3, 21, 6, 36, 20, #45979), "What's new?"])

>>> q.save()
#True
>>> q.id
#1

>>> q.pub_date
#datetime.datetime(2019, 3, 21, 6, 36, 20, 45979)

>>> q.question_text = "What's up?"
>>> q.save()
#It took 3.67ms to execute the query ('UPDATE "questions" SET "question_text" = ?, #"updated_at" = ? WHERE "id" = ?', ["What's up?", '2019-03-20 21:38:01.456334', 1])
#True

>>> Question.all()
#It took 0.15ms to execute the query ('SELECT * FROM "questions"', [])
#<orator.orm.collection.Collection object at 0x10c88c9e8> 
```

在丹戈。

```
#polls/models.py 
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text 
```

在 Masonite

```
# """Question Model"""
import datetime

from config.database import Model

class Question(Model):
    """Question Model"""
    __fillable__ = ['question_text', 'pub_date']

    def __repr__(self):
        return self.question_text 
```

```
"""Choice Model"""

from config.database import Model

class Choice(Model):
    """Choice Model"""
    __fillable__ = ['question_id', 'choice_text', 'votes']

    def __repr__(self):
        return self.choice_text 
```

在姜戈

```
# import datetime

from django.db import models
from django.utils import timezone

class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1) 
```

在 Masonite

```
# """Question Model"""
import datetime

from config.database import Model

class Question(Model):
    """Question Model"""
    __fillable__ = ['question_text', 'pub_date']
    __dates__ = ['pub_date']

    def __repr__(self):
        return self.question_text

    def was_published_recently(self):
        return self.pub_date >= datetime.datetime.now() - datetime.timedelta(days=1) 
```

在丹戈。

```
>>> from polls.models import Choice, Question

# Make sure our __str__() addition worked.
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by
# keyword arguments.
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# Get the question that was published this year.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a
# shortcut for primary-key exact lookups.
# The following is identical to Question.objects.get(id=1).
>>> Question.objects.get(pk=1)
<Question: What's up?>

# Make sure our custom method worked.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Give the Question a couple of Choices. The create call constructs a new
# Choice object, does the INSERT statement, adds the choice to the set
# of available choices and returns the new Choice object. Django creates
# a set to hold the "other side" of a ForeignKey relation
# (e.g. a question's choice) which can be accessed via the API.
>>> q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.
>>> q.choice_set.all()
<QuerySet []>

# Create three choices.
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
#<Choice: The sky>

>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.
>>> c.question
#<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.

>>> q.choice_set.all()
#<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

>>> q.choice_set.count()
#3

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).

>>> Choice.objects.filter(question__pub_date__year=current_year)
#<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.

>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete() 
```

在 Masonite

```
>>> from app.Question import Question
>>> from app.Choice import Choice

>>> Question.all()
#It took 0.14ms to execute the query ('SELECT * FROM "questions"', [])
#<orator.orm.collection.Collection object at 0x10c990d30>

>>> Question.find(1) #Question.find(id=1) works the same
#It took 0.26ms to execute the query ('SELECT * FROM "questions" WHERE #"questions"."id" = ? LIMIT 1', [1])
#What's up?

Question.where_raw('question_text LIKE ?', ["What%"]).get()
#It took 2.68ms to execute the query ('SELECT * FROM "questions" WHERE question_text LIKE ?', ['What%'])
#<orator.orm.collection.Collection object at 0x10c9aa9e8>

>>> import datetime
>>> current_year = datetime.datetime.now().year

>>> Question.find(id=2).get()
#It took 0.09ms to execute the query ('SELECT * FROM "questions" WHERE #"questions"."id" = ? LIMIT 1', [2])
#Traceback (most recent call last):
#  File "<console>", line 1, in <module>
#AttributeError: 'NoneType' object has no attribute 'get' 
```

为了处理[关系](https://orator-orm.com/docs/0.9/orm.html#relationships)
我们必须像
一样编辑我们的模型

```
"""Question Model"""
import datetime
from config.database import Model
from orator.orm import has_many

class Question(Model):
    """Question Model"""

    __fillable__ = ['question_text', 'pub_date']

    __dates__ = ['pub_date']

    def __repr__(self):
        return self.question_text

    def was_published_recently(self):
        return self.pub_date >= datetime.datetime.now() - datetime.timedelta(days=1)

    @has_many
    def choice(self):
        from app.Choice import Choice
        return Choice 
```

然后才能处理好关系。

```
>>> q = Question.find(id=1)
#It took 0.1ms to execute the query ('SELECT * FROM "questions" WHERE "questions"."id" = ? LIMIT 1', [1]) 
>>> q
#What's up? 
>>> q.was_published_recently()
#True 
>>> q.choice().get()
#It took 47.28ms to execute the query ('SELECT * FROM "choices" WHERE #"choices"."question_id" = ?', [1])
#<orator.orm.collection.Collection object at 0x10da62908> 

>>> q.choice().create(choice_text='Not much', votes=0)
#It took 44.38ms to execute the query ('INSERT INTO "choices" ("choice_text", #"created_at", "question_id", "updated_at") VALUES (?, ?, ?, ?)', ['Not much', #'2019-03-20 22:16:52.997981', 1, '2019-03-20 22:16:52.997981'])
#Not much 
>>> q.choice().create(choice_text='The sky', votes=0)
#It took 3.53ms to execute the query ('INSERT INTO "choices" ("choice_text", #"created_at", "question_id", "updated_at") VALUES (?, ?, ?, ?)', ['The sky', #'2019-03-20 22:17:11.626357', 1, '2019-03-20 22:17:11.626357'])
#The sky 
>>> q.choice().create(choice_text='Just hacking again', votes=0)
#It took 295.54ms to execute the query ('INSERT INTO "choices" ("choice_text", #"created_at", "question_id", "updated_at") VALUES (?, ?, ?, ?)', ['Just #hacking again', '2019-03-20 22:17:47.761326', 1, '2019-03-20 #22:17:47.761326'])
#Just hacking again 

>>> q.choice().count()
#It took 0.17ms to execute the query ('SELECT COUNT(*) AS aggregate FROM #"choices" WHERE "choices"."question_id" = ?', [1])
#3 
>>> import datetime
>>> current_year = datetime.datetime.now().year 
```

# 待办事宜

想办法做

```
# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete() 
```