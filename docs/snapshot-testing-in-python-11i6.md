# Python 中的快照测试

> 原文：<https://dev.to/leahein/snapshot-testing-in-python-11i6>

[![snapshot](img/787f5ed4b012b0e57abb9171d3799fb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bz3Z__5C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fvb7u7auyxllq0okkuwe.jpg)

您是否曾经发现自己编写了冗长乏味的测试，接受了一个大型数据结构，并测试了一个相似的、经过验证的数据结构的输出？无论是将字典转换为命名元组还是序列化对象，如果您曾经尝试过为 API 测试数据模型或 GraphQL 模型，您可能都经历过这种情况。

编写测试并复制我们期望的相同响应可能是一项单调乏味的任务。快照测试是减轻测试这些数据结构的痛苦的好方法。随着我们的 API 的发展，我们需要知道我们的更改何时引入了任何突破性的更改，而快照测试将准确地告诉我们这一点。

### 什么是快照测试？

简单来说，`A snapshot is a single state of your output, saved in a file.`

> 一个典型的移动应用程序快照测试用例呈现一个 UI 组件，截取一个屏幕截图，然后将其与测试中存储的参考图像进行比较。如果两个图像不匹配，测试将失败:要么是意外的更改，要么是屏幕截图需要更新到 UI 组件的新版本。

在测试您的 API 或数据结构时，可以采用类似的方法。除了呈现图形用户界面，您还可以使用一个测试呈现器，比如 [`SnapshotTest`](https://github.com/syrusakbary/snapshottest) 库，来为您的 API 响应快速生成一个可序列化的值。

快照让我们可以轻而易举地编写所有这些测试，因为它会在第一次执行测试时自动为我们创建快照。然后，以后每次运行测试时，它都会将输出与文件快照进行比较。如果它们不同，我们的测试将会失败，并立即提醒我们这一变化。

### 如何使用`SnapshotTest`库

首先，我们安装库:

```
pip install snapshottest 
```

Enter fullscreen mode Exit fullscreen mode

我将在下面提供 python 内置的`unittest`库和`pytest`的代码片段，但是您可以将它用于您选择的任何测试框架。

我们将从一个我们想要测试的简单的命名元组开始。

```
'''Our Clown Code'''

from collections import namedtuple

Clown = namedtuple('Clown', 'name', 'nose_type')) 
```

Enter fullscreen mode Exit fullscreen mode

带单元测试:

```
 from snapshottest import TestCase

class TestClown(TestCase):

    def test_clown(self):
      clown = Clown('bozo', 'red and round')                                  
      self.assertMatchSnapshot(clown)  

# >> python -m unittest 
```

Enter fullscreen mode Exit fullscreen mode

同 pytest:

```
import pytest

def test_clown(snapshot):
      clown = Clown('bozo', 'red and round')                                  
      snapshot.assert_match(clown) 

# >> pytest 
```

Enter fullscreen mode Exit fullscreen mode

这将在首次执行测试时创建一个快照目录和一个快照文件。

```
>> pytest
=================== SnapshotTest summary ===================
1 snapshot passed.
1 snapshot written in 1 test suites. 
```

Enter fullscreen mode Exit fullscreen mode

下次我们运行测试时，它会将我们的测试结果与快照进行比较。因为什么都没有改变，我们得到了下面的结果:

```
>> pytest
=================== SnapshotTest summary ===================

1 snapshot passed. 
```

Enter fullscreen mode Exit fullscreen mode

如果我们的数据结构或输出发生变化，快照测试将会失败。然后，我们可以比较这些变化，并确定它们是否有效。要更新快照，我们可以运行:

```
pytest --snapshot-update 
```

Enter fullscreen mode Exit fullscreen mode

[![snapshot](img/dcab07ad7b2d52077b03f4449d8c554f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lV_OG4dy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbmepicuu727oqm1i03d.jpg)

显然，上面是一个超级简单的测试用例，我们并没有真正测试任何逻辑。但是同样的模式也可以应用于测试 API 请求。

```
import requests

def test_my_api(snapshot):
    response = request.get('my_api.com')
    snapshot.assert_match(response) 
```

Enter fullscreen mode Exit fullscreen mode

或者测试一个 GraphQL API

```
from graphene.test import Client

def test_hey(snapshot):
    client = Client(my_schema)
    response = client.execute('''{ hey }''')
    snapshot.assert_match(response) 
```

Enter fullscreen mode Exit fullscreen mode

快照测试可能无法取代单元测试或集成测试，但它是快速有效地确定 API 或数据序列化状态是否发生变化的好方法。