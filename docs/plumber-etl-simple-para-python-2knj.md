# 管道工:ETL simple para Python

> 原文：<https://dev.to/yorodm/plumber-etl-simple-para-python-2knj>

为其中一个我已完成工作的项目实施功能时，我需要执行典型的 ETL 进程，该进程从 x 源中提取内容，通过一个或多个过滤器，然后将其加载到我们的文档数据库中。

现在，虽然有诸如[【pet】](https://petl.readthedocs.io)和[【bono bo】](https://www.bonobo-project.org)等优质框架涵盖这一领域，但我遇到了以下问题:

1.  项目中有很多代码 *legacy* 。
2.  除 ETL 部分之外，处理作业的复杂度相当高，因此增加它并不是最合适的。
3.  ETL 组件不会自行运行，而是作为解决方案的一部分运行。
4.  我们添加的大多数数据使用非常规格式。

虽然看起来很简单，但这四点促使我为 ETL 组件创建了自己的“*mini framework”*，该框架易于与现有代码集成，且对数据格式没有任何假设， [plumber](https://github.com/yorodm/plumber) 。

## 管道工的特征。

对于框架的开发，我在很大程度上借鉴了我以前与 [kiba](https://www.kiba-etl.org) 的经历，这种首饰之一总是来自 Ruby 社区。Kiba 利用 Ruby 的优点创建，让开发者以自己认为正确的方式关注数据。在[梅花](https://github.com/yorodm/plumber)中，这种想法体现在四个概念上:

1.  提取程序(用于访问数据源)。
2.  Transformers(处理生成的数据)。
3.  写入者(数据持续存在)。
4.  管道(控制整个过程)。

### 提取器。

un 提取器是为管道(pipe)输出数据的类或函数，数据可以来自任何来源和任何形式，提取器独立于过程的其馀部分。

虽然可以使用函数作为提取器，但大多数情况下推荐的是继承类别`plumber.pipe.Extractor`并实现方法`read`、`setup`和`cleanup`。可以将多个提取器合并为一个提取器，以生成具有 decada 值的字符串(元组)。

### 变形金刚。

Un **变压器**是任何标有`@transformer`或`plumber.pipe.Transformer`分类的功能。您可以合并多个转换器，但不能合并处理链

### 作家。

终于把**作家**从`plumber.pipe.Writer`继承下来了。由于他们通常获得外部资源，所以除了`write`之外，还需要实施`setup`和`cleanup`方法。

所有元素均使用注释[【pep 484】](https://www.python.org/dev/peps/pep-0484/)。

## 一个简单的例子:

创建框架的一个使用案例是从专用格式的文件中获取记录列表。文件内容大致如下:

1.  “客户机 id”字段。从位置 0 开始，长度为 1 到 20。
2.  事务处理标识符字段。从位置 22 开始。是数字，长度为 8。
3.  相关事务处理标识符字段。从位置 32 开始。长度为 8，是可选的，并且不包含空格。
4.  事务处理原因字段。从位置 42 开始，长度在 10 到 50 之间。它是字母数字型的，包含空格，没有边界。

让我们看一个如何处理这些文件的例子:

```
from plumber import pipe # API síncrona. 
@pipe.extractor
def read_file():
    file_name = os.environ['FILENAME']
    with open(file_name) as f:
        for x in f.readlines():
            yield process_line(x)

@pipe.transformer
def csvfy(element):
    yield ','.join(map(str,element))

class SaveData(pipe.Writer):

    def __init__ (self, filename):
        self.filename = filename

    def setup(self):
        self._file = open(f,'w')

    def cleanup(self):
        self._file.close()

    def write(x):
        self._file.write(x)

tuberia = pipe.Pipe(
    read_file,
    csvfy,
    SaveData("prueba.csv")
)
tuberia.run() 
```

Enter fullscreen mode Exit fullscreen mode

## 还有待做吗？

现在 [plumber](https://github.com/yorodm/plumber) 问世了，是时候把它变成中等体面的 ETL 框架了。接下来的步骤是:

1.  增加了将**作家**作为具有集成上下文管理功能的功能的可能性。
2.  能够轻松初始化提取器。
3.  改进错误处理。
4.  并行性和/或并行性。