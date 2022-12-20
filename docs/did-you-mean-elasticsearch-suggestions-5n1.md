# 你的意思是:弹性搜索建议？

> 原文：<https://dev.to/raoulmeyer/did-you-mean-elasticsearch-suggestions-5n1>

人们一定会犯错别字。你可能知道谷歌是如何处理这些情况的:搜索引擎会建议一个正确的搜索词，或者如果什么也找不到，甚至会自动纠正你搜索的词。这将用户留在他们的搜索流中。

通过优化搜索查询的匹配，只能解决这么多问题。这篇文章向你展示了如何使用 suggester 查询类型在 Elasticsearch 中找到正确的搜索词，以及如何有效地应用它。如果你想自己尝试一下，看看这个[例子回购](https://github.com/RaoulMeyer/elasticsearch-didyoumean)。

## 问题

你能做的最糟糕的事情是，当你实际上知道要显示什么结果时，却没有显示任何结果。这直接伤害了用户，因为他们要么需要修改他们的搜索词，要么会认为他们正在寻找的商品根本不存在。当没有任何结果时，用户至少会感到恼火，在许多情况下，他们可能会完全放弃对某样东西的搜索。

## 设置索引

Elasticsearch 提供了广泛的[建议功能](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters.html)，允许你自动完成用户输入。今天我们将使用*短语提示器*来查找与一些输入搜索项相关的搜索项。

为了让*短语 Suggester* 工作，我们需要为它提供一个生成器列表。这些生成器中的每一个都将为单个术语生成建议。然后，*短语建议器*会以一种巧妙的方式将这些建议组合成一个对整个输入的修正。

例如，假设您有一个文本为“哈利波特海报”的文档。如果用户现在搜索“波特”，你可以更正为“波特”或“海报”。没有上下文，你不知道哪一个更好。如果一个用户现在搜索“哈利波特”，你肯定知道该给出哪个建议:“波特”。

为了考虑上下文，*短语建议器*使用指定字段的[瓦片式](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-shingle-tokenfilter.html)版本来正确地建议术语的组合。它可以使用数据的瓦片式表示来确定校正后的单词彼此相邻出现的常见程度。

根据你如何配置带状疱疹过滤器，建议者将能够建议更短或更长的句子。大多数情况下，一个合理的分析器应该这样设置:

```
...  "analysis":  {  "analyzer":  {  "shingle":  {  "type":  "custom",  "tokenizer":  "standard",  "filter":  [  "standard",  "lowercase",  "shingle_filter"  ]  }  },  "filter":  {  "shingle_filter":  {  "type":  "shingle",  "min_shingle_size":  2,  "max_shingle_size":  3  }  }  }  ... 
```

Enter fullscreen mode Exit fullscreen mode

我们需要将这个分析器应用到一个字段，所以我们将它添加到映射中。在这个例子中，我们有一个包含书籍的索引。为了能够基于 title 字段建议术语，我们将设置以下映射:

```
...  "mappings":  {  "book":  {  "properties":  {  "title":  {  "type":  "text",  "fields":  {  "shingle":  {  "type":  "text",  "analyzer":  "shingle"  }  }  },  "author":  {  "type":  "keyword"  },  "year":  {  "type":  "integer"  }  }  }  }  ... 
```

Enter fullscreen mode Exit fullscreen mode

这将我们刚刚定义的瓦片分析器添加到标题字段中。

## 查询相关搜索词

既然我们已经建立了索引，我们将向它添加一些文档。在示例存储库中，只需运行`npm start`就可以做到这一点。我们现在可以执行下面的查询来获取用户可能输入的任何文本的建议:

```
{  "suggest":  {  "text":  "prisoner of axaban",  "phrase_suggester":  {  "phrase":  {  "field":  "title.shingle",  "confidence":  0.0,  "direct_generator":  [  {  "field":  "title.shingle"  }  ]  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一的非默认值是信心。默认情况下，置信度将被设置为 1.0，但是对于这个用例来说，这将对结果造成太大的限制。

运行这个查询将给出一个选项列表:

```
...  "options":  [  {  "text":  "prisoner of azkaban",  "score":  0.22856912  },  {  "text":  "prisoner of axaban",  "score":  0.040878013  }  ]  ... 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，显示了整个搜索词的正确版本。然而，由于置信度设置得如此之低，原始搜索词也显示为一个选项。对于这个查询，我们不能保证每个选项都会实际显示结果。幸运的是，有一种方法可以过滤掉那些不能改善我们当前无结果状态的选项。

## 修剪选项

*短语提示器*接受一个 collate 字段，该字段包含一个脚本化的查询，将为每个返回的选项执行该查询。通过将它添加到我们的建议查询中，我们将为每个选项获得一个布尔值，告诉我们该选项是否返回了 collate 查询的结果。

在我们的例子中，如果建议的选项 fuzzy 与 title 字段匹配，我们可能已经很高兴了。以下查询包含带有模糊匹配的比较查询:

```
{  "suggest":  {  "text":  "prisoner of axaban",  "simple_phrase":  {  "phrase":  {  "field":  "title.shingle",  "confidence":  0.0,  "direct_generator":  [  {  "field":  "title.shingle"  }  ],  "collate":  {  "query":  {  "source":  {  "match":  {  "title":  {  "query":  "{{suggestion}}",  "fuzziness":  "1",  "operator":  "and"  }  }  }  },  "prune":  "true"  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

运行这个查询会将`collate_match`字段添加到响应:

```
"options":  [  {  "text":  "prisoner of azkaban",  "score":  0.22856912,  "collate_match":  true  },  {  "text":  "prisoner of axaban",  "score":  0.040878013,  "collate_match":  false  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以根据`collate_match`筛选出选项。在示例中，我们只在搜索查询没有返回结果时运行这个建议查询。如果我们找到匹配的建议，我们用该建议替换当前的搜索词，并再次执行搜索查询。

## 改进建议

根据您的用例，您可能想要使用置信度参数。如果你想要自动改变用户输入的功能，默认值 1.0 是明智的。如果您希望根据自己的逻辑(例如，很少或没有结果)或根据返回的分数来确定是否向用户显示建议，则较低的值是有意义的。

默认情况下，建议最多纠正一个术语。您可以通过将`max_errors`参数设置为 2 或更大，或者设置为输入术语计数的一部分(例如，将其设置为 0.5 将允许 50%的术语被纠正)来增加这个值。如果输入大部分时间包含多个术语，将此参数设置为 2 可能会很有用。如该参数的文档中所述，将该值设置得太高会对性能产生负面影响。

## 结论

Elasticsearch 建议功能是一个非常强大和通用的工具。大部分艰苦的工作都被抽象掉了，但是通过调整查询接受的众多参数，您仍然可以对结果进行很大的控制。我可以通过多种方式看到有人使用这一功能:

*   如果可信度足够高，自动更正搜索查询中的错误
*   在自动完成/提前键入中显示拼写更正
*   基于索引中已有的数据检测 CMS 中可能的拼写错误
*   在大型数据集中查找人为输入错误

如果你想尝试这些，可以从查看 [Github repo](https://github.com/RaoulMeyer/elasticsearch-didyoumean) 开始。