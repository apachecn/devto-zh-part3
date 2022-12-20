# Python 中日期时间和时间戳之间的转换

> 原文：<https://dev.to/jcmorrow/converting-between-datetimes-and-timestamps-in-python-2fag>

时区是编程中的一个常见问题。在 Paloma，我写了很多代码，这些代码与事情发生的时间和顺序有关，这意味着我要做大量的时间戳比较。Python 处理时区的方法旨在使这个过程尽可能无痛，而且它(大部分)成功了。Python 通过将日期时间分为两种不同的类型来实现这一点:

*   一种类型的日期时间是朴素类型。Naive datetimes 没有所在时区的概念，它们只具有相对于彼此的意义。如果您的所有时间都来自同一个时区(例如，如果您只将本地机器时间用于 CLI ),这种方法非常有效

*   另一种类型称为感知日期时间。python 文档将感知日期时间描述为:

> 充分了解适用的算法和政治时间调整，如时区和夏令时信息，以确定自身相对于其他感知对象的位置。

问题是您不能比较 aware 和 naive datetimes，否则您会得到以下错误:

```
TypeError: can't compare offset-naive and offset-aware datetimes 
```

所以你应该从这些策略中选择一个，尽可能坚持下去。

在 Paloma，我们几乎从不在服务器端使用 aware datetimes。我们用 UTC 存储所有日期时间，只在需要向用户显示某些内容时才执行时区逻辑。这种方法通常会避开所有可能的时区 gotchyas，但我最近遇到了一个 bug，让我开始怀疑自己的理智。

我们经常得到带有 unix 时间时间戳的事件，所以我们必须在 unix 时间戳和 python 日期时间之间进行转换。例如，考虑将日期时间转换为 unix 时间戳的代码:

```
>>> from datetime import datetime
>>> now = datetime.utcnow()
>>> now
datetime(2018, 11, 15, 3, 24, 18, 997071)
>>> now.timestamp()
1542223458.997071 
```

现在，我刚刚从一个日期时间中获得了一个时间戳，该日期时间是通过调用 python 的`datetime.utcnow()`创建的。我预计，如果我们把它当作一个 UTC 时间戳，我们将得到与开始时相同的日期时间。让我们来试试:

```
>>> import pytz
>>> datetime.fromtimestamp(now.timestamp(), pytz.utc)
datetime(2018, 11, 14, 19, 24, 18, 997071, tzinfo=<UTC>) 
```

啊哦，这次不一样了！这是怎么回事？

棘手的部分是:`utcnow`将当前的 utc 时间作为一个简单的对象返回，但是 timestamp 需要知道这个简单的对象是在 UTC 还是在您的本地时区才能工作。在这种情况下，它将假设它是在您的本地时区，如果您不生活在 UTC，并且您试图像上面一样往返您的时间戳，这将是一个主要问题。

我通过在我们需要在原始日期时间和时间戳之间进行转换时添加一个额外的步骤来解决这个问题:转换到位于 UTC 中的感知日期时间。基本上，这个过程现在看起来是这样的:

`Naive Datetime -> Aware Datetime (UTC) -> Timestamp`

反之亦然:

`Timestamp -> Aware Datetime (UTC) -> Naive Datetime`

用 python 代码来说:

```
>>> from datetime import datetime
>>> import pytz
>>> now = datetime.now(pytz.utc)
>>> now
datetime(2018, 11, 15, 11, 57, 33, 320996, tzinfo=<UTC>)
>>> now.timestamp()
1542283053.320996
>>> datetime.fromtimestamp(now.timestamp(), pytz.utc)
datetime(2018, 11, 15, 11, 57, 33, 320996, tzinfo=<UTC>) 
```

当然，如果我们每次都必须记住这样做，那么我们的代码库就会失败，所以我添加了几个助手方法:

```
import pytz
from datetime import datetime

def datetime_from_timestamp(ts):
    return datetime.fromtimestamp(ts, pytz.utc).replace(tzinfo=None)

def timestamp_from_datetime(dt):
    return dt.replace(tzinfo=pytz.utc).timestamp() 
```

现在，我们可以轻松地在各种格式之间来回转换，而无需改变我们所引用的时间:

```
>>> from datetime import datetime
>>> import pytz
>>> now = datetime.now(pytz.utc)
>>> now
datetime(2018, 11, 15, 11, 57, 33, 320996, tzinfo=<UTC>)
>>> now.timestamp()
1542283053.320996
>>> datetime.fromtimestamp(now.timestamp(), pytz.utc)
datetime(2018, 11, 15, 11, 57, 33, 320996, tzinfo=<UTC>)
>>> assert datetime_from_timestamp(timestamp_from_datetime(now)) == now 
```

所以，概括一下:大多数时候，在任何地方都可以使用天真的约会时间，一切都很美好。但是，在简单日期时间和其他时间表示形式(如时间戳)之间进行转换时，您应该小心，它可能不会按照您预期的方式运行！在进行这种类型的转换时，更安全的做法是创建一个具有时区意识的中间形式，以确保不假定任何时区信息。