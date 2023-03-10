# 用 NLTK 的 WordNet 语料库进行词汇分析

> 原文：<https://dev.to/navierula/word-analysis-with-nltks-wordnet-corpus-54n7>

语言分析可以用许多不同的方法通过计算来完成。神经网络和深度学习，有人吗？😊尽管将您的文档扔进算法并让它为您提供重要的特征很有趣，但单词或句子级别的特征分析也是分析文本的一种很好的方法——尤其是如果您刚刚开始使用自然语言处理(NLP)的话。

我们将使用 Python 的 nltk 库来分析使用 nltk 的 WordNet 语料库的单词。

#### 什么是 WordNet？

WordNet 是一种特殊的英语词典。当你使用 WordNet 查找一个单词时，它不仅会返回该单词的正确定义，还会返回与该单词相关的所有含义。

这些感官组合在一起，被称为 synsets。每个同义词集代表一个与单词相关联的独特概念。

例如，“蝙蝠”这个词可以是名词，指夜间活动的动物；然而，“bat”的另一个意思可能是一个动词，指的是红袜队球员用来击出全垒打的一个物体。现在，让我们来看一些如何在 Python 中使用 WordNet 语料库的例子。

#### 快速安装注意事项

如果您没有安装 nltk 库，请打开您的终端并键入命令:

```
pip install nltk 
```

现在您已经安装了 nltk，我们可以像这样导入 WordNet 语料库:

```
from nltk.corpus import wordnet 
```

#### 查找单词

既然我们在这里使用字典，那么下一个合乎逻辑的步骤就是继续查找一个单词。既然风在吹，我感觉很积极，让我们继续查找单词“breezy”

```
breezy_syns = wordnet.synsets("breezy")
breezy_syns
>>> [Synset('breezy.s.01'), Synset('blowy.s.01')] 
```

我们可以看到“breezy”有两个意思。让我们剖析第一种意义，以便更好地理解每个同义词集包含的信息种类。我们可以通过在其名称处索引第一个元素来做到这一点。

```
breezy_syns[0].name()
>>> 'breezy.s.01' 
```

“Breezy”的第一感觉包含了 WordNet 在线词典中编码的同类信息:

**breezy** :与感觉相关的词(这里是“breezy”的字面意思，也是最常见的意思)。
**s** :单词的词性(即形容词)
**01** :单词在 synset 中的顺序(这里是第一个，各位！)

我们也可以查找特定词义的定义以及与之相关的例子。

```
breezy_syns[0].definition()
>>> fresh and animated

breezy_syns[0].examples()
>>> ['her breezy nature'] 
```

#### 同义词和反义词

除了查找单词之外，WordNet 还可以用于导出特定单词的所有同义词或反义词。当我在大学学习文学，试图找到一个最荒谬的形容词来描述我这个非常平凡的名词时，这肯定对我很有用。因为 breezy 不是一个很受欢迎的词，所以让我们查找可能包含更多变化的同义词，比如单词“small”

```
for sim in wordnet.synsets('small'):
    print(sim.name(), sim.lemma_names())

>>> small.n.01 ['small']
>>> small.n.02 ['small']
>>> small.a.01 ['small', 'little']
>>> minor.s.10 ['minor', 'modest', 'small', 'small-scale', 'pocket-size', 'pocket-sized']
>>> little.s.03 ['little', 'small']
>>> small.s.04 ['small']
>>> humble.s.01 ['humble', 'low', 'lowly', 'modest', 'small']
>>> little.s.07 ['little', 'minuscule', 'small']
>>> little.s.05 ['little', 'small']
>>> small.s.08 ['small']
>>> modest.s.02 ['modest', 'small']
>>> belittled.s.01 ['belittled', 'diminished', 'small']
>>> small.r.01 ['small'] 
```

在上面的代码中，我遍历了与“small”相关联的每个 synset，并返回它的名称表示和与这些表示相关联的同义词。“小”在这个意义上当然词汇量大！

当然，我们也想看看“small”的反义词。

```
for opp in wordnet.synsets('small'):
    if opp.lemmas()[0].antonyms() != []:
        print(opp.name(),opp.lemmas()[0].antonyms()[0].name())

>>> small.a.01 large
>>> small.r.01 big 
```

当然也有可能某个特定的词义不存在反义词，所以我们要小心不要返回任何空列表(因此，上面的条件)。在这里，我将返回所有与“small's”相关的反义词，它们是第一个也是最常见的词义。

#### 字相似之处

WordNet 还更进了一步，它允许你执行简单的单词比较来检测两个词义之间的相似性。这在诸如单词生成、相似性检测或搜索/相关性的 NLP 任务中可能是有用的。我们来试试吧！

```
dog = wordnet.synset('dog.n.01')
cat = wordnet.synset('cat.n.01')

collar = wordnet.synset('collar.n.01')

dog.wup_similarity(cat)
>>> 0.8571428571428571

dog.wup_similarity(collar)
>>> 0.47058823529411764 
```

我们可以看到“狗”和“猫”非常相似，因为它们很可能在上下文中同时出现。也有可能它们属于同一类哺乳动物或宠物，当然，狗是更好的选择！

“Dog”和“collar”虽然在对话中看似相互关联，但实际含义却不尽相同。

看到 WordNet 中单词比较返回的概率总是很有趣。如果你尝试用一些单词来表达，我很乐意看到你的结果或听到你的想法！

<center>***</center>

好了，暂时就这样吧！我希望我已经鼓励了你去探索 WordNet 所提供的一些东西。我自己仍在努力学习，并想与你分享我迄今为止学到的东西。😊