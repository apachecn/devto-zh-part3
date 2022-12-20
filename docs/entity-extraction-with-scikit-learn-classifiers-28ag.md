# 用 Scikit-learn 分类器进行实体提取

> 原文：<https://dev.to/sematext/entity-extraction-with-scikit-learn-classifiers-28ag>

## 什么是实体提取？

实体提取是确定查询应该以哪些字段为目标的过程，而不是总是命中所有字段。例如:当用户键入**苹果 iPhone** 时，如何辨别意图是运行**公司:苹果**和**产品:iPhone** ？

## 实体抽取是分类问题吗？

通常，[当你想到实体提取时，你会想到上下文](https://sematext.com/blog/entity-extraction-opennlp-tutorial/):在**诺基亚 3310 是一部旧手机**像**是**或 **an** 这样的词是强烈的指示，在它们之前，我们有一个主题。电子商务查询是一个特例:我们通常没有什么上下文。在 Activate 的[我们的“产品搜索的实体提取”演示中，我们认为，如果你只有**诺基亚 3310** ，那么确定**诺基亚**是制造商而 **3310** 是型号就是一个分类问题。在这篇文章中，我们将探索解决这个分类问题的方法之一:训练和使用](https://www.slideshare.net/sematext/entity-extraction-for-product-search) [Scikit-learn](https://scikit-learn.org) 分类模型。

## 什么是 Scikit-learn，如何获取？

[Scikit-learn](https://scikit-learn.org) 是一个流行的机器学习库。它是用 Python 写的，所以要获得它，你只需:

```
pip install sklearn
pip install numpy 
```

我们还将安装 [NumPy](https://docs.scipy.org/doc/numpy/index.html) ，因为我们需要以 [NumPy 数组](https://docs.scipy.org/doc/numpy/reference/generated/numpy.array.html)的形式提供训练集。

## 功能选择

在实现任何东西之前，我们需要弄清楚哪些特性与分类相关。特性选择是一个持续的过程，但是我们需要一些东西来开始。在[激活示例](https://github.com/sematext/activate/tree/master/sklearn)中，我们使用了三个特征:词频、位数和空格数。我们假设，通常情况下，与型号相比，制造商名称在我们的索引中出现的频率更高，因为型号是非常独特的。我们希望型号中有更多的数字，制造商名称中有更多的空格。最根本的问题是，什么能帮助人们区分一个实体和另一个实体。在这种情况下，制造商从型号。您可以通过特性获得创意:[实体是否与制造商或型号的字典相匹配](https://sematext.com/blog/using-solr-tag-text/)？查询有多长？我们的实体位于哪个位置？因为电子商务中有常见的构造，比如厂商+型号(**诺基亚 3310** )或者型号+代( **iPhone 3GS** ，如果我们墨守成规的话)。

## 训练和测试集

### 数据清理

当涉及到训练和测试一个模型时，古老的“垃圾进，垃圾出”的说法在这里同样适用。您将希望按照您认为合适的方式来管理您的数据:小写和词干在许多实体提取设置中非常有用。就像常规搜索一样:)在测试或应用模型时，您会注意到一些“实体”跨越了多个单词。你可以用 word [n-grams](https://en.wikipedia.org/wiki/N-gram) 来解决这个问题。比如在**苹果 Mac Book** 中，你会拿**苹果**、 **mac** 、 **book** 、**苹果 mac** 和 **mac book** ，期望得到**苹果**为制造商， **mac** 和 **mac book** 为型号。你可以选择大一点的(mac book)或者两者都选(mac + mac book，但“mac book”的排名更高)，这取决于你如何平衡[精度和召回](https://en.wikipedia.org/wiki/Precision_and_recall)。

### 将实体解析成特征数组

当训练一个模型时，你不需要输入 sci kit——学习实际的单词，而是这些单词的特征。您将需要代码，在给定查询(或实体)的情况下，可以生成特性数组。在我们的例子中，对于**诺基亚**，你将有 0 个数字，0 个空格和它在你的索引中的频率。在我们的示例代码中，我们从文件中读取数据。我们假设每一行都包含一个实体，我们还使用该文件来判断频率:如果我们遇到一个实体 N 次，我们将得到频率 N。最后，我们返回一个字典，其中实体是键，值是该实体的特征数组。

```
 def read_into_feature_dict(file):
     with open(file) as le_file:
         le_dict = {}
         for line in le_file:
             line = line.strip("\\n")
             if line not in le_dict:
                 # other features besides frequency
                 digits = sum(c.isdigit() for c in line)
                 spaces = sum(c.isspace() for c in line)
                 # initialize an array of [frequency, digits, spaces]. Frequency is initially 1
                 le_dict[line] = [1,digits, spaces]
             else:
                 # increment frequency if we met this before
                 le_dict[line][0] = le_dict[line][0] + 1
         return le_dict 
```

## 训练一个模特

为了训练模型，我们将只需要特征数组的列表，而不需要键。这个特征数组列表是我们的训练集(X)，但是我们还需要每个实体的标签(y)。在我们的例子中，标签是制造商或型号:

```
# we have a file with manufacturers and one with models. Read them into dictionaries mfr_feature_dict = read_into_feature_dict("mfrs")
model_feature_dict = read_into_feature_dict("models")

# from the dictionaries, we get only the feature arrays and add them to one list training = []
for i in mfr_feature_dict:
    training.append(mfr_feature_dict[i])
for i in model_feature_dict:
    training.append(model_feature_dict[i])

# make the list a NumPy array. That’s what Scikit-learn requires X = np.array(training)

# add training labels. We know that we first added manufacturers, then models y = []
for i in range(len(mfr_feature_dict)):
    y.append("mfr")
for i in range(len(model_feature_dict)):
    y.append("model") 
```

此时，我们可以选择一个模型并对其进行训练。Scikit-learn 自带各种[分类器](https://scikit-learn.org/stable/supervised_learning.html)。从简单的线性[支持向量机](https://scikit-learn.org/stable/modules/svm.html#classification)，就像我们在这个例子中使用的一样，到[决策树](https://scikit-learn.org/stable/modules/tree.html#classification)和[感知器](https://scikit-learn.org/stable/modules/neural_networks_supervised.html#classification)(你在我们的 [OpenNLP 教程](https://sematext.com/blog/entity-extraction-opennlp-tutorial/)中看到的同一类算法)。当然，虽然参数不同，但您可以用相似的方式使用它们。有了我们的训练 X 和 y，以及选定的算法，我们就可以尝试了。对于线性 [SVC](https://en.wikipedia.org/wiki/Support-vector_machine#Support-vector_clustering_(SVC)) ，代码可以是:

```
# select the algorithm. Here, linear SVC clf = svm.SVC(kernel='linear', C = 1.0)
# train it clf.fit(X, y) 
```

这里， **[C](https://medium.com/all-things-ai/in-depth-parameter-tuning-for-svc-758215394769)** [是误差项](https://medium.com/all-things-ai/in-depth-parameter-tuning-for-svc-758215394769)的惩罚参数。直觉告诉我们，C 越高，您的模型就越适合您的训练集，但这也可能导致过度拟合。还有[其他 SVC 参数](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html#sklearn.svm.SVC)，比如迭代次数。

## 利用模型预测实体

此时，我们可以使用我们的模型进行实体提取。或者至少我们可以测试一下。为此，我们可以从一些测试样本中构建一个测试 X，并使用分类器的 **predict()** 函数来获得建议的实体:

```
def test_from_file(test_file):
  test_X = []

  # same function that we used for the training set: read manufacturers/codes from a file
  # then turn them into a dictionary of entities to feature arrays
  test_dict = read_into_feature_dict(test_file)

  # concatenate feature arrays into our X
  for feature_set in test_dict:
    test_X.append(test_dict[feature_set])
  print(test_dict.keys())
  print(test_X)

  # use our model to predict entities for each entity
  print(clf.predict(test_X)) 
```

## 结论和下一步措施

通过精心选择的特征，分类是从电子商务查询中提取实体的良好解决方案。我们在这里用 Scikit-learn 展示了一个例子，但是当然，还有其他好的选择。SpaCy 是其中之一，我们将很快在这里发布另一个操作指南！如果你觉得这些东西令人兴奋，请加入我们:[我们正在全球招聘](https://sematext.com/jobs/)。如果您需要实体提取、相关性调整或任何其他关于您的搜索基础设施的帮助，请[联系](https://sematext.com/contact/)，因为我们提供:

*   [Solr、Elasticsearch 和 Elastic Stack 咨询](https://sematext.com/consulting/)
*   [Solr、弹性搜索和弹性堆栈生产支持](https://sematext.com/support/)
*   [Solr、Elasticsearch 和 Elastic Stack 培训课程](https://sematext.com/training/)(现场和远程、公共和私人)
*   [不仅针对 Solr 和 Elasticsearch，还针对其他应用程序(如 Kafka、Zookeeper)、](https://sematext.com/cloud/)[基础设施](https://sematext.com/spm)和[容器](https://sematext.com/docker)进行监控、日志集中和跟踪

如果你想通过 Solr 或 Elasticsearch 提高你的工作效率，看看**两个有用的备忘单**，当你使用这两个开源搜索引擎中的任何一个时，它们都可以帮助你提高工作效率并节省时间:

*   如何访问所有新的 Solr 特性——运行 Solr、数据操作、搜索、分面等。[在这里下载你的](https://sematext.com/solr-cheat-sheet/?utm_medium=blogpost&utm_source=blogpost&utm_campaign=scikit-learn-classifiers-blogpost&utm_content=blog-solr-cheat-sheet)
*   每个开发人员都需要的关键弹性搜索操作——索引创建、映射操作、索引 API 等等！[在这里下载你的](https://sematext.com/elasticsearch-developer-cheat-sheet/?utm_medium=blogpost&utm_source=blogpost&utm_campaign=scikit-learn-classifiers-blogpost&utm_content=blog-elasticsearch-developer-cheatsheet)