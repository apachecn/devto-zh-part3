# 基于空间的实体提取

> 原文：<https://dev.to/sematext/entity-extraction-with-spacy-fi>

## 什么是实体提取？

在搜索的上下文中，实体提取是计算出查询应该以哪些字段为目标的过程，而不是总是命中所有字段。我们希望在搜索中包含实体提取的原因是为了提高精确度。例如:当用户输入苹果 iPhone 时，我们如何判断他的意图是运行**公司:苹果**和**产品:iPhone** ？而不是带回来苹果形状的手机贴纸？

## 什么是 spaCy？

spaCy 是一个 Python 框架，可以做很多[自然语言处理](https://en.wikipedia.org/wiki/Natural_language_processing) (NLP)任务。[命名实体抽取](https://spacy.io/usage/linguistic-features#named-entities) (NER)就是其中之一，另外还有[文本分类](https://spacy.io/usage/training#textcat)、[词性标注](https://spacy.io/usage/linguistic-features#pos-tagging)等等。

如果这听起来很熟悉，那可能是因为我们之前写过一个不同的 Python 框架，它可以帮助我们进行实体提取: [Scikit-learn](https://sematext.com/blog/entity-extraction-scikit-learn-classifiers/) 。尽管 Scikit-learn 更多的是机器学习工具的集合，而不是 NLP 框架。就功能而言，spaCy 更接近于 [OpenNLP](https://sematext.com/blog/entity-extraction-opennlp-tutorial/) 。在我们的 [Activate 2018 演示](https://www.slideshare.net/sematext/entity-extraction-for-product-search)中，我们使用了这三种方法进行实体提取。

获得空间就像:

```
pip install spacy 
```

Enter fullscreen mode Exit fullscreen mode

在这篇文章中，我们将使用一个预先构建的模型来提取实体，然后我们将构建自己的模型。

## 使用预先建立的模型

spaCy 附带了针对许多语言的预建模型。例如，要得到英文的，你要做:

```
python -m spacy download en_core_web_sm 
```

Enter fullscreen mode Exit fullscreen mode

然后，在您的 Python 应用程序中，只需要加载它:

```
nlp = spacy.load('en_core_web_sm') 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以用它来提取实体。在我们的[激活示例](https://github.com/sematext/activate/blob/master/spacy/def.py)中，我们做到了:

```
doc = nlp(u"#bbuzz 2016: Rafał Kuć - Running High Performance And Fault Tolerant Elasticsearch")
for entity in doc.ents:
    print(entity.label_, ' | ', entity.text) 
```

Enter fullscreen mode Exit fullscreen mode

哪些输出:

```
MONEY | #bbuzz
DATE | 2016
PERSON | Rafał Kuć - Running High 
```

Enter fullscreen mode Exit fullscreen mode

对于这个特殊的例子，这个结果充其量是“近似的”。2016 年确实是个日期，但 [#bbuzz](https://berlinbuzzwords.de/) 不是钱。我怀疑[Rafa](https://sematext.com/blog/author/kucrafal/)在演讲时情绪高涨。

对于这个用例，我们需要构建自己的模型。

## 训练新模型

为了训练一个新的模型，我们首先需要创建一个定义我们如何处理数据的管道。在这种情况下，我们希望提取实体。然后，我们将通过这个管道运行测试数据来训练一个模型。一旦模型被训练，我们也可以用它从新数据中提取实体。

让我们放大每一步。

### 空间管道

使用 spaCy，您可以做的不仅仅是实体提取。例如，在提取实体之前，您可能需要[预处理文本](https://spacy.io/usage/linguistic-features#tokenization)，例如通过词干处理。或者我们可能想做[词性标注](https://spacy.io/usage/linguistic-features#pos-tagging):这个词是动词还是名词？

对于本教程的范围，我们将创建一个空模型，给它一个名称，然后向它添加一个简单的管道。这个简单的管道将只提取命名实体(NER):

```
nlp = spacy.blank('en') # new, empty model. Let’s say it’s for the English language
nlp.vocab.vectors.name = 'example_model_training' # give a name to our list of vectors
# add NER pipeline
ner = nlp.create_pipe('ner') # our pipeline would just do NER
nlp.add_pipe(ner, last=True) # we add the pipeline to the model 
```

Enter fullscreen mode Exit fullscreen mode

### 数据和标签

为了训练模型，我们需要一些训练数据。在产品搜索的情况下，这些将是查询，其中我们预先标记实体。例如:

```
DATA = \[
  (u"Search Analytics: Business Value & BigData NoSQL Backend, Otis Gospodnetic ", {'entities': [ (58,75,'PERSON') ] }),
  (u"Introduction to Elasticsearch by Radu ", {'entities': [ (16,29,'TECH'), (32, 36, 'PERSON') ] }),
  # …
] 
```

Enter fullscreen mode Exit fullscreen mode

我们的训练数据有几个特点:

*   文本本身是 Unicode 的
*   实体数组包含一个元组列表。每个元组都是从文本中标记的实体
*   每个元组包含三个元素:开始偏移量、结束偏移量和实体名称

### 训练模型

在训练之前，我们需要让我们的模型知道可能的实体。为此，我们添加了所有我们知道的标签:

```
nlp.entity.add_label('PERSON')
nlp.entity.add_label('TECH')
# ... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以开始训练了。我们需要分配模型，并通过我们的[模型](https://spacy.io/api/language)的 [begin_training()](https://spacy.io/api/language#begin_training) 方法获得一个优化器:

```
optimizer = nlp.begin_training() 
```

Enter fullscreen mode Exit fullscreen mode

然后我们用我们的训练数据更新模型。每个文本及其注释(那些被标记的实体)将被传递给我们的模型的 [update()函数。连同新创建的优化器:](https://spacy.io/api/language#update)

```
nlp.update([text], [annotations], sgd=optimizer) 
```

Enter fullscreen mode Exit fullscreen mode

在我们的[激活示例](https://github.com/sematext/activate/blob/master/spacy/train.py)中，因为我们几乎没有训练数据，所以我们只是以随机的顺序循环几次:

```
for i in range(20):
    random.shuffle(DATA)
    for text, annotations in DATA:
        nlp.update([text], [annotations], sgd=optimizer) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在我们有了一个为我们自己的用例构建的模型。

### 预测实体

我们刚刚构建的模型已经加载到内存中。如果你不想每次都训练它，你可以[保存到磁盘](https://spacy.io/api/language#to_disk)，需要的时候[加载](https://spacy.io/api/language#from_disk)。加载模型后，您将使用它来预测实体，就像使用预构建的模型一样:

```
doc = nlp(u"#bbuzz 2016: Rafał Kuć - Running High Performance And Fault Tolerant Elasticsearch")
for entity in doc.ents:
    print(entity.label_, ' | ', entity.text) 
```

Enter fullscreen mode Exit fullscreen mode

即使有了这个小数据集，结果**通常也比默认模型**看起来更好:

```
PERSON | Rafał Kuć
TECH | Elasticsearch 
```

Enter fullscreen mode Exit fullscreen mode

我提到**通常是**，因为在不同的运行中，由于随机化，模型看起来是不同的。最终，如果你想要准确的结果，没有什么可以替代训练集的大小。除非 2016 年的[拉法](https://sematext.com/blog/author/kucrafal/)真的有问题，因为有时我会得到:

```
PERSON | Rafał Kuć

TECH | High 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
结论和下一步

就像我们之前展示的 [OpenNLP 示例](https://sematext.com/blog/entity-extraction-opennlp-tutorial/)中一样，spaCy 附带了预构建的模型，可以轻松构建自己的模型。它还附带了一个[命令行培训工具](https://spacy.io/api/cli#train)。也就是说，它的可配置性更差——或者至少你不像在一个专门构建的工具中那样拥有所有的选项，比如 [Scikit-learn](https://sematext.com/blog/entity-extraction-scikit-learn-classifiers) 。对于实体提取，spaCy 将使用一个[卷积神经网络](https://en.wikipedia.org/wiki/Convolutional_neural_network)，但如果需要，您可以[插入自己的模型](https://spacy.io/api/entityrecognizer#model)。

如果你觉得这些东西令人兴奋，请加入我们:[我们正在全球招聘](https://sematext.com/jobs/)。如果您需要实体提取、相关性调整或任何其他关于您的搜索基础设施的帮助，请[联系](https://sematext.com/contact/)，因为我们提供:

*   [Solr、Elasticsearch 和 Elastic Stack 咨询](https://sematext.com/consulting/)
*   [Solr、弹性搜索和弹性堆栈生产支持](https://sematext.com/support/)
*   [Solr、Elasticsearch 和 Elastic Stack 培训课程](https://sematext.com/training/)(现场和远程、公共和私人)
*   [不仅针对 Solr 和 Elasticsearch，还针对其他应用程序(如 Kafka、Zookeeper)、](https://sematext.com/cloud/)[基础设施](https://sematext.com/spm)和[容器](https://sematext.com/docker)进行监控、日志集中和跟踪