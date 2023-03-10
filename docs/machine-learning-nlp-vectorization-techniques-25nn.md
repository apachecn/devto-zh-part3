# 机器学习—自然语言处理矢量化技术

> 原文：<https://dev.to/bhavaniravi/machine-learning-nlp-vectorization-techniques-25nn>

### 机器学习— NLP 矢量化技术

每当你开始使用任何涉及文本的 ML 算法时，你都应该把文本转换成一串数字。这是显而易见的，因为 ML 算法通常建立一个概率模型(这是数学)，而数学只能处理数字。

> 将 NLP 文本转换成数字的过程在机器学习中称为矢量化。

#### 单词词汇

词汇是你正在处理的所有单词的列表。如果您的数据集可以包含 1000 个文档，那么您的单词词汇表将是文档中唯一单词的列表。对于一个通用的聊天机器人，你的词汇表将包含语言词典中的所有单词。对于一个客户成功机器人来说，它将会有一种语言理解，以及与你提供的产品或服务有关的词语。

### 包字(鞠躬)

最简单和最强大的矢量化技术。给定一串句子，BoW 创建一个单词表，并与表示其对文档贡献的分数相关联。

评分可以基于以下任何评分技术进行

1.  字数—(出现的次数)
2.  词频—(出现次数/总字数)
3.  TFIDF 矢量器—出现最少的单词携带了关于文档的最多信息

**应用** : BoW 用于垃圾邮件过滤。给定一封电子邮件和邮件中的单词频率，最简单的方法是找出出现最多的单词是否是垃圾邮件。TFIDF 用于主题建模和文档标记。

BoW 还发现了计算机视觉的一个特点，即图像特征被视为一个单词向量，并保存在一个稀疏矩阵中。

### Word2Vec

Word2Vec 是一种将单词表示为一串数字的策略。这些数字(向量)不是随机分配的，而是以这样一种方式分配的，即两个相似的词在向量空间中靠得更近。

[![](img/5485a6505b57ff749afad97eb3835465.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nnn6zNoO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A70iOJhTnYxj7Wc08TMbFaw.png)

#### 申请

1.  词性标注
2.  实体提取
3.  聊天机器人

[![](img/63c7c617cfeb0103b7f7554dc6115427.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DkaL2fbI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AJlO7wQeJDLTUTw8q)

### **参考文献**

1.  [https://en.wikipedia.org/wiki/Bag-of-words_model](https://en.wikipedia.org/wiki/Bag-of-words_model)
2.  [https://en . Wikipedia . org/wiki/Bag-of-words _ model _ in _ computer _ vision](https://en.wikipedia.org/wiki/Bag-of-words_model_in_computer_vision)
3.  [https://sci kit-learn . org/stable/modules/feature _ extraction . html # text-feature-extraction](https://scikit-learn.org/stable/modules/feature_extraction.html#text-feature-extraction)
4.  [https://blog . acolyer . org/2016/04/21/the-amazing-power-of-word-vectors/](https://blog.acolyer.org/2016/04/21/the-amazing-power-of-word-vectors/)