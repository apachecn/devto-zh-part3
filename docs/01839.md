# Django JSONFields 的聚合

> 原文：<https://dev.to/saschalalala/aggregation-in-django-jsonfields-4kg5>

这是我在 dev.to 上的第一篇帖子，我认为它更多的是给自己的一个提示，也许有人和我一样对此感兴趣。

# 我与姜戈和波斯格里斯正在做(将要做)的事情

我目前正在分析一些 Twitter JSON 数据。我为此使用了不同的库和工具，例如 pandas 和 networkx 来查看数据和创建网络，还有 Kibana 和 Elasticsearch 来进行一些更定量的分析和可视化(每天的 Tweets 和类似的东西)。

然而，我确定了一些我想集中分析的用户，阅读一些内容，查看一些用户级的可视化，诸如此类。在 jupyter 笔记本上做这些事情非常麻烦，因为它缺乏专注于这些事情的 web 应用程序的易用性。

# 为什么是 JSONField，为什么是 Postgres

首先，Django 与 Postgres 配合得非常好，我不想在项目中集成额外的 NoSQL 数据库。此外，我不知道 Django 与 MongoDB 的配合有多好。其次，我不知道 NoSQL 的数据库是如何“工作”的。我使用 MySQL 和 Postgres 多年，即使我不是专家，我通常也能相处得很好。第三点(和第一点有点混淆)，我想使用 ORM，不想仅仅为了数据检索而学习一些新的语法。

# 数据准备

这部分我不想赘述，因为我有一个相当大的自制工具链。最重要的部分是，最后我有一个熊猫数据帧，可以导出到一个平面 json 结构。我决定不创建一个可以与这个 json 结构一起使用的 Django 模型，因为我很确定数据结构在将来会经常改变，我不想仅仅因为我在数据结构中添加了一些字段就编写迁移和修改我的核心应用程序。

数据结构如下:

```
{  "created_at":  15588051070000,  "favorite_count":  4,  "id":  <unique_id>,  "is_quote":  null,  "is_reply":  null,  "is_retweet":  null,  "quote_status_id":  0,  "quote_text":  null,  "quote_user_id":  null,  "quote_user_name":  null,  "reply_count":  0,  "reply_status_id":  0,  "reply_user_id":  null,  "reply_user_name":  null,  "retweet_count":  5,  "retweet_status_id":  0,  "retweet_text":  null,  "retweet_user_id":  null,  "retweet_user_name":  null,  "text":  "<tweet_text>",  "text_is_extended":  false,  "user_id":  <user_id>,  "user_name":  "<user_name>"  } 
```

我最感兴趣的是文本、创建时间和转发量。为了计算这些值，需要进行一些注释和过滤。

# 我的第一个重要查询

当然，当涉及到数据聚合/分组/过滤等时，我希望我的数据库来承担重任。如果我不想让数据库这样做，我可以使用 json 平面文件并将它们加载到 python 字典、pandas 数据帧或 javascript 对象中。

我需要的 webapp 首页信息如下:

所有用户名及其各自的推文量，以及按此总和排序的推文总和。

在一个“普通的”Django 应用程序中，这并不难做到，我认为您应该这样做:

```
# I wrote this from the top of my head so if it is wrong,
# please correct me in the comments 
tweets = Tweet.objects.values("user_name")
    .annotate(
        tweet_count=Count("user_name"),
        retweets=Sum("retweet_count")
    )
    .order_by("-retweets") 
```

当然，我不能这么做，因为我的 Tweet 模型没有`Tweet.user_name`或类似的东西。看起来就是这样:

```
class Tweet(models.Model):
    name = models.CharField(max_length=200)
    data = JSONField()
    created_at = models.DateTimeField(null=True) 
```

我需要的所有数据都在`Tweet.data`中进行了 json 格式化。我在 Django 文档中读到了 JSON field([https://docs . Django project . com/en/2.2/ref/contrib/postgres/fields/# key-index-and-path-lookups](https://docs.djangoproject.com/en/2.2/ref/contrib/postgres/fields/#key-index-and-path-lookups))并且我已经知道我可以做类似
的事情

```
Tweet.objects.filter(data__user_name="whoever") 
```

这真的很酷，因为这或多或少与您在使用普通关系数据库模型时使用的语法完全相同。所以我想，我也可以在我的注释中这样做。把`Sum("retweet_count")`改成`Sum("data__retweet_count")`就好了。

事实证明并非如此，因为这些聚合函数只“看到”对象的`data`这个东西，而看不到它的内容。因此，虽然计数这些东西是可行的，但是用这种方法对`data__retweet_count`中的数字求和是不可行的。

在阅读了 stackoverflow 上一些使用 RAWSql 查询之类的非常复杂的解决方案之后，我发现我正在使用的解决方案非常有效:

```
Tweet.objects.annotate(
    user_name=Cast(
        KeyTextTransform("user_name", "data"), models.TextField()
    )
)
.values("user_name")
.annotate(
    tweet_count=Count("data"),
    retweets=Sum(
        Cast(
            KeyTextTransform("retweet_count", "data"), models.IntegerField()
        )
    ),
)
.order_by("-retweets") 
```

因此，虽然这看起来比上面的查询复杂得多，但实际上并不复杂，我将一个接一个地解释那里发生的事情。

这里最重要的是你要从`django.contrib.postgres.fields.jsonb`导入的`KeyTextTransform`。它所做的是从指定的 json 对象中提取指定的键(在 ORM 级别上，我不知道它是如何工作的)。

所以第一个带有 user_name 的注释基本上只是`Get the user_name from the json thing as a string (Textfield) and name it user_name`。我这样做的唯一原因是，如果我不这样做，这个值将在结果数据结构中被称为`data__user_name`,这是我不想要的。

下一个注释首先是 tweet count，它只是我的数据库中用户名出现的计数器，所以我可以把所有东西都放在那里。retweets 注释基本上与 user_name 相同，但它转换为整数而不是文本，并对其中的数字求和。

因此，总结一下这个查询:唯一添加到上面的是 json 数据结构中的键查找，以及将该值转换为我需要的数据类型。仅此而已。

# 最终结果

我将这个查询放在一个管理器中，我的 Tweet 模型如下所示:

```
import logging
import pytz

from django.conf import settings
from django.contrib.postgres.fields import JSONField
from django.contrib.postgres.fields.jsonb import KeyTextTransform
from django.db import models
from django.db.models import Count, Sum
from django.db.models.functions import Cast

from django.utils import timezone

logger = logging.getLogger(__name__)

class TweetManager(models.Manager):
    def counts_only(self):
        return (
            Tweet.objects.annotate(
                user_name=Cast(
                    KeyTextTransform("user_name", "data"), models.TextField()
                )
            )
            .values("user_name")
            .annotate(
                tweet_count=Count("data"),
                retweets=Sum(
                    Cast(
                        KeyTextTransform("retweet_count", "data"), models.IntegerField()
                    )
                ),
            )
            .order_by("-retweets")
        )

class Tweet(models.Model):
    name = models.CharField(max_length=200)
    data = JSONField()
    created_at = models.DateTimeField(null=True)
    objects = TweetManager()

    def __str__(self):
        return self.name

    def save(self, *args, **kwargs):
        try:
            self.name = f"{self.data['user_name']}_{self.data['id']}"
            # the creation datetime is stored as miliseconds timestamp
            naive_created_at = timezone.datetime.fromtimestamp(
                self.data["created_at"] / 1000
            )
            self.created_at = pytz.timezone(settings.TIME_ZONE).localize(
                naive_created_at, is_dst=None
            )
        except Exception as e:
            logger.error(e)
        super().save(*args, **kwargs) 
```

我现在可以称它为我的观点:

```
Tweet.objects.counts_only() 
```

并得到一个结果，我可以把它传递到前端，并在我的模板或 javascript 中使用它，而不需要重新计算任何东西:

```
[{'user_name': 'user_0', 'tweet_count': 25, 'retweets': 2760},
{'user_name': 'user_1', 'tweet_count': 1, 'retweets': 891},
{'user_name': 'user_2', 'tweet_count': 165, 'retweets': 2265},
{'user_name': 'user_3', 'tweet_count': 12, 'retweets': 1769},
{'user_name': 'user_4', 'tweet_count': 59, 'retweets': 1663},
{'user_name': 'user_5', 'tweet_count': 6, 'retweets': 1657},
{'user_name': 'user_6', 'tweet_count': 8, 'retweets': 1420},
{'user_name': 'user_7', 'tweet_count': 26, 'retweets': 1186},
{'user_name': 'user_8', 'tweet_count': 32, 'retweets': 1076},
{'user_name': 'user_9', 'tweet_count': 473, 'retweets': 932}] 
```