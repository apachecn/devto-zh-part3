# 优化 Django ORM

> 原文：<https://dev.to/adamghill/optimize-the-django-orm-53hb>

最近一直在优化一些比预期慢的功能。和大多数 MVP 一样，最初的迭代是让*的东西*开始工作并在那里出现。查看 [Scout APM](https://scoutapp.com/) 发现一些数据库查询很慢，包括几个`n+1`查询。`n+1`查询的发生是因为我在一组模型上循环，并为每个模型更新或选择相同的内容。我的目标是减少任何重复的查询，并通过将简单、直接的操作重构为更高性能的等价操作来尽可能多地挤出性能。

老实说，现在通读代码稍微复杂了一点，但是我把我的用例的时间减少了一半，而没有改变服务器或数据库的任何东西。

# 使用 ORM，卢克

Django 的主要优势之一是内置的模型和对象关系映射器(ORM)。它为模型的数据操作提供了一个快速使用的通用接口，并且可以非常容易地处理大多数查询。一旦你理解了语法，它也可以做一些复杂的 SQL。

很容易快速建造。还很容易导致比您意识到的更多(昂贵的)SQL 调用。

# 再见，模特们

这里有一些示例模型，将用于说明下面的一些概念。

```
# models.py class Author(models.Model):
    name = models.CharField(max_length=50)

class Book(models.Model):
    author = models.ForeignKey(Author, related_name="books", on_delete=models.PROTECT)
    title = models.CharField(max_length=255) 
```

Enter fullscreen mode Exit fullscreen mode

# 展示 sql(第 1 部分)

因为 SQL 调用*被*抽象在一个简单的 API 后面，所以很容易导致比您意识到的更多的 SQL 调用。您可以使用 QuerySet 上的`query`属性来检索一个接近的近似值，但是要注意它是一个“[不透明表示](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet)”的警告。

```
books = Book.objects.all()
print("books.query", books.query) 
```

Enter fullscreen mode Exit fullscreen mode

# 展示 sql(第二部分)

您还可以将`django.db.logging`添加到您配置的记录器中，以查看生成的 SQL 被打印到控制台。

```
"loggers": {
    "django.db.backends": {
        "level": "DEBUG",
        "handlers': ["console", ],
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 展示 sql(第三部分)

您还可以打印出 Django 存储在数据库连接上的时间和生成的 SQL。

```
from django.db import connection

books = Book.objects.all()
print("connection.queries", connection.queries) 
```

Enter fullscreen mode Exit fullscreen mode

# 一个工具栏来统治一切

如果您的代码是从一个视图中调用的，那么开始解密生成的 SQL 的最简单的方法就是安装 [Django 调试工具栏](https://django-debug-toolbar.readthedocs.io/en/latest/)。DDT 提供了一个非常有用的诊断工具，可以显示所有正在运行的 SQL 查询，有多少是相似的，有多少是重复的。您还可以查看每个 SQL 查询的查询计划，并探究为什么它可能很慢。

# 选择并预取*所有相关的*

需要意识到的一点是，Django 的 ORM 在默认情况下是相当懒惰的。在请求结果之前，它不会运行查询(无论是在代码中还是直接在视图中)。除非需要，否则它也不会通过外键连接模型。这些都是有益的优化，但是如果你没有意识到，它们会咬你。

```
# views.py def index(request):
    books = Book.objects.all()

    return render(request, { "books": books }) 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- index.html -->{% raw %}
{% for book in books %}
Book Author: {{ book.author.name }}<br />
{% endfor %}{% endraw %} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，`index.html`中的`for loop`中的每本书都会为作者的名字再次调用数据库。因此，将有一个数据库调用来检索所有书籍的集合，然后对列表中的每本书进行一个额外的数据库调用。

防止额外数据库调用的方法是使用 [`select_related`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#select-related) 强制 Django 加入另一个模型一次，如果使用了该关系，则防止后续调用。

更新视图代码以使用一个`select_related`会将相同 Django 模板的 sql 调用总数减少到只有 1 次。

```
# views.py def index(request):
    books = Book.objects.select_related("author").all()

    return render(request, { "books": books }) 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，`select_related`不起作用，但`prefetch_related`会起作用。Django 文档中有[更多关于何时使用`prefetch_related`的细节](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#prefetch-related)。

# 当心模型的实例化

当 Django ORM 创建一个`QuerySet`时，它从数据库中获取数据并填充模型。但是，如果您不需要模型，有一些方法可以跳过不必要的构建。

[`values_list`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#values-list) 将返回所有指定列的元组列表。特别有用的是`flat=True`关键字参数，如果只指定了一个字段，它将返回一个扁平列表。

```
# get a list of book ids to use later book_ids = Book.objects.all().values_list("id", flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用 [`values`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.values) 创建一个包含稍后可能需要的数据对的字典。例如，如果我需要博客 id 和它们的 URL:

```
# get a dictionary of book id->title book_ids_to_titles = {b.get("id"): b.get("title") for b in Book.objects.all().values("id", "title")} 
```

Enter fullscreen mode Exit fullscreen mode

获取所有图书 id:`book_ids_to_titles.keys()`。要获得所有称号:`book_ids_to_titles.values()`。

与此有点关系的是， [`bidict`](https://bidict.readthedocs.io/en/master/) 是一种很好的从字典的值中检索关键字的简单方法，反之亦然(相对于保存 2 个字典)。

```
book_ids_to_titles = bidict({
    "1": "The Sandman",
    "2": "Good Omens",
    "3": "Coraline",
})

assert book_ids_to_titles["1"] == book_ids_to_titles.inv["The Sandman"] 
```

Enter fullscreen mode Exit fullscreen mode

# 过滤 id 让世界运转

使用`filter`转换成 SQL 中的`WHERE`子句，搜索整数[几乎总是比搜索 Postgres 中的字符串](https://stackoverflow.com/questions/2346920/sql-select-speed-int-vs-varchar)快。所以，`Book.objects.filter(id__in=book_ids)`会比`Book.objects.filter(title__in=book_titles)`表现的稍微好一点。

# 唯和而从

[`Only`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.only) 和 [`Defer`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.defer) 是镜像相反的方法，目的相同，只为模型检索特定的字段。 [`Only`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.only) 的工作原理是选择指定的数据库字段，但不填充任何非指定字段。 [`Defer`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.defer) 的工作方式相反，所以这些字段不会包含在 SELECT 语句中。

然而，Django 文档中的这个注释告诉我们:

> 当您仔细分析了您的查询并准确理解了*您需要的*信息，并且已经测量了返回您需要的字段和模型的完整字段集之间的差异将是显著的时，它们提供了优化。

# 注解并继续

对于某些代码，我在一个循环中获取列表中每个模型的计数。

```
for author in Author.objects.all():
    book_count = author.books.count()
    print(f"{book_count} books by {author.name}") 
```

Enter fullscreen mode Exit fullscreen mode

这将为每个作者创建一个 SQL `SELECT`语句。相反，使用一个 [`annotation`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.annotate) 将创建一个 SQL 查询。

```
author_counts = (
    Author.objects
    .annotate(book_count=Count("book__id"))
    .values("author__name", "book_count")
)

for obj in author_counts:
    print(f"{obj.get('book_count')} books by {obj.get('author__name')}") 
```

Enter fullscreen mode Exit fullscreen mode

[`Aggregation`](https://docs.djangoproject.com/en/2.1/topics/db/aggregation/) 是 [`annotation`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.annotate) 的简单版本，如果你想计算一个列表中所有对象的值(例如，从一个模型列表中获得最大 id)。 [`Annotation`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.annotate) 如果你想计算一个列表中每个模型的值并得到输出，这个选项很有用。

# 散装*粉碎*！错误，创建

使用 [`bulk_create`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#bulk-create) 可以用一个查询创建多个对象。使用它有一些注意事项，不幸的是，在 insert 之后，您没有得到一个有用的 id 列表。但是，对于简单的用例，它工作得很好。

```
author = Author(name="Neil Gaiman")
author.save()

Book.objects.bulk_create([
    Book(title="Neverwhere", author=author),
    Book(title="The Graveyard Book", author=author),
    Book(title="The Ocean at the End of Lane", author=author),
]) 
```

Enter fullscreen mode Exit fullscreen mode

# 我们要散装*你*起来

[`update`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#update) 是`QuerySet`上的一个方法，因此您可以检索一组对象，并使用一个 SQL 查询更新所有对象上的一个字段。但是，如果你想用不同的字段值更新一组模型 [`django-bulk-update`](https://github.com/aykut/django-bulk-update) 就会派上用场。它自动为一组模型更新创建一个 SQL 语句，即使它们有不同的值。

```
from django.utils import timezone
from django_bulk_update.helper import bulk_update

books = Book.objects.all()
for book in books:
    book.title = f"{book.title} - {timezone.now}"

# generates 1 sql query to update all books bulk_update(books, update_fields=['title']) 
```

Enter fullscreen mode Exit fullscreen mode

# 让你流汗(现在每个人都在使用 Sql)

如果您实在想不出让 Django ORM 生成高性能 SQL 的方法，那么 [`raw sql`](https://docs.djangoproject.com/en/2.1/ref/models/expressions/#django.db.models.expressions.RawSQL) 总是可用的，尽管通常不建议使用它，除非您不得不这样做。

# 穿上丽兹

Django 文档通常很有帮助，会为您提供关于上述每种技术的更深入的细节。如果你知道任何其他方法来从 Django 中获得最大的性能，我很乐意在 [@adamghill](https://twitter.com/adamghill) 上听到这些方法。

*最初发表于[adamghill.com](https://adamghill.com/django/python/2018/12/02/optimize-the-django-orm.html)。*