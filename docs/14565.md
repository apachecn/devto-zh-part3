# 几分钟内用 Python 写一个简单的玩具数据库！

> 原文：<https://dev.to/bauripalash/write-a-simple-toy-database-in-python-within-minutes-3e8o>

MySQL，PostgreSQL，Oracle，Redis...还有更多你想得到的...数据库确实是人类文明进程中的一项重要技术。今天，我们可以看到**数据**是多么的有价值，为了保证它们的安全和稳定，这就是数据库的由来！

我们可以看到数据库有多重要。很长一段时间以来，我一直在考虑创建自己的玩具数据库，就像理查德·费曼所说的那样，只是为了理解、玩和试验它

> ### What I can't create, I don't understand

所以没有任何进一步的讨论，让我们进入有趣的部分...编码...

对于这个玩具数据库，我们将使用 **Python** (我最喜欢的❤️)，我将这个数据库命名为 **FooBarDB** (我找不到任何名称；))，但是你想怎么叫都行！

所以首先让我们导入一些必要的 python 库，它们已经在 Python 标准库中可用

```
import json
import os 
```

Enter fullscreen mode Exit fullscreen mode

是的，我们只需要这两个 lib！`json`因为我们的数据库将基于 json 和`os`的一些路径相关的东西！

现在让我们用一些非常基本的函数来定义主类`FoobarDB`，我将在下面解释它们。

```
class FoobarDB(object):
    def __init__(self , location):
        self.location = os.path.expanduser(location)
        self.load(self.location)

    def load(self , location):
        if os.path.exists(location):
            self._load()
        else:
            self.db = {}
        return True

    def _load(self):
        self.db = json.load(open(self.location , "r"))

    def dumpdb(self):
        try:
            json.dump(self.db , open(self.location, "w+"))
            return True
        except:
            return False 
```

Enter fullscreen mode Exit fullscreen mode

这里我们用一个`__init__`函数定义了我们的主类，每当创建一个 Foobar 数据库时，我们只需要传递数据库的位置。
在第一个`__init__`函数中，我们采用位置参数并用用户的主目录替换`~`或`~user`，使其按预期的方式工作，最后将它放入`self.location`变量中，以便以后在同一个类函数中访问它。最后，我们调用`load`函数，将`self.location`作为参数传递。

```
. . . .
    def load(self , location):
        if os.path.exists(location):
            self._load()
        else:
            self.db = {}
        return True
. . . . 
```

Enter fullscreen mode Exit fullscreen mode

在下一个`load`函数中，我们将数据库的位置作为参数，然后检查数据库是否存在，如果存在，我们简单地用`_load()`函数加载它(下面解释),否则我们创建一个空的内存 JSON 对象。最后，成功时返回 true。

```
. . . . 

    def _load(self):
        self.db = json.load(open(self.location , "r"))
. . . . 
```

Enter fullscreen mode Exit fullscreen mode

在`_load`函数中，我们只是简单地从`self.location`中存储的位置打开数据库文件，然后将它转换成 JSON 对象并加载到`self.db`变量中。

```
. . . .
    def dumpdb(self):
        try:
            json.dump(self.db , open(self.location, "w+"))
            return True
        except:
            return False

. . . . 
```

Enter fullscreen mode Exit fullscreen mode

最后是`dumpdb`函数，它的名字说明了它的作用，它基本上只是从`self.db`变量中获取内存中的数据库(实际上是一个 JSON 对象)并将其保存在数据库文件中！如果保存成功则返回**真**，否则返回**假**

但是，等一下！😐如果数据库不能存储和检索数据，它就没有用，不是吗？😉让我们也去添加它们...😎

```
. . . .
    def set(self , key , value):
        try:
            self.db[str(key)] = value
            self.dumpdb()
            return True
        except Exception as e:
            print("[X] Error Saving Values to Database : " + str(e))
            return False

    def get(self , key):
        try:
            return self.db[key]
        except KeyError:
            print("No Value Can Be Found for " + str(key))  
            return False

    def delete(self , key):
        if not key in self.db:
            return False
        del self.db[key]
        self.dumpdb()
        return True
. . . . 
```

Enter fullscreen mode Exit fullscreen mode

`set`的功能是向数据库添加数据，因为我们的数据库是一个简单的基于键值的数据库，我们将只把`key`和`value`作为参数。首先，我们将尝试将键和值添加到数据库中，然后保存数据库，如果一切正常，它将返回 True，否则它将打印一条错误消息并返回 False(我们不希望它在每次出现错误时崩溃并删除我们的数据😎).

```
. . . .
    def get(self, key):
        try:
            return self.db[key]
        except KeyError:
            return False
. . . . 
```

Enter fullscreen mode Exit fullscreen mode

`get`是一个简单的函数，我们将`key`作为一个参数，并尝试从数据库中返回链接到键的值，否则将返回一条消息。

```
. . . .
    def delete(self , key):
        if not key in self.db:
            return False
        del self.db[key]
        self.dumpdb()
        return True

. . . . 
```

Enter fullscreen mode Exit fullscreen mode

`delete`功能是从数据库中删除一个键及其值。首先，我们确保数据库中有这个键，如果没有，我们返回 False，否则，我们用内置的`del`删除这个键，它会自动删除这个键的值。接下来，我们保存数据库并返回 false。

现在，您可能会想，如果我创建了一个大型数据库，并希望重置它，该怎么办？理论上，我们可以使用`delete`但是它不实用，而且非常耗时！⏳⏳
所以我们可以创建一个函数来完成这项任务...

```
. . . . 

    def resetdb(self):
        self.db={}
        self.dumpdb()
        return True
. . . . 
```

Enter fullscreen mode Exit fullscreen mode

下面是重置数据库的函数，`resetdb`！这很简单，首先我们要做的是用一个空的 JSON 对象重新分配内存数据库，然后保存它！就是这样！我们的数据库现在又被清理干净了！

完整的源代码在这里👉 [bauripalash/foobardb](https://github.com/bauripalash/foobardb)

* * *

就是这样朋友们！我们创建了自己的**玩具数据库**！！🎉🎉
实际上 **FoobarDB** 只是一个数据库的简单演示，它就像一个廉价的 DIY 玩具，你可以以任何你想要的方式改进它！还可以根据需要添加很多其他功能！

我希望，你喜欢！请在下面的评论中告诉我你的建议、想法或我犯的错误！👇

在社交网站上关注/Ping 我👉[脸书](https://facebook.com/bauripalash)，[推特](https://twitter.com/bauripalash)， [Instagram](https://instagram.com/bauripalash)

谢谢大家！回头见！

* * *

如果你喜欢我的作品(我的文章、故事、软件、研究等等),考虑给我买一杯☕咖啡🤗