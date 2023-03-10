# Elasticsearch 全文查询解释，针对人类🤓

> 原文：<https://dev.to/ajsharp/elasticsearch-full-text-queries-explained-for-humans--1kfk>

*   [ElasticSearch 全文查询文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/full-text-queries.html)

ES 有不同的查询类型。快速总结:

*   `match` -标准全文查询
*   `match_phrase` -短语匹配。就像你在谷歌上给一个术语加引号一样。
*   `match_phrase_prefix`——可怜人的自动补全。
*   `multi_match` -多字段`match`。
*   `common`(此处未涉及)-考虑停用词(如“the”)。
*   `query_string`(此处未涉及)-专家模式。可以在查询字符串中使用 AND|OR|NOT 和多字段搜索。
*   `simple_query_string`(此处未涉及)-更简单“更健壮”地向最终用户公开。(简单如何更健壮？似乎有悖直觉。没人知道。)

## `match` -标准全文查询

*   [文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html)

默认情况下，进行近似精确的单词相等匹配。

**何时使用**:如果你在一个大的文本域上进行全文搜索，比如这篇文章的正文。

在文本块中匹配单词或短语的良好默认设置，例如在大型文本字段、描述字段等中搜索，例如在某人的 Twitter 个人资料中搜索短语。

不适用于单个单词或短语的部分匹配，例如某人的 Twitter 用户名的部分匹配(参见下面的模糊性)。

**模糊匹配**
通过指定一个 [levenstein 编辑距离](http://en.wikipedia.org/wiki/Levenshtein_distance)允许[模糊匹配](https://www.elastic.co/guide/en/elasticsearch/reference/current/common-options.html#fuzziness)(例如错别字容差)，这是使一个字符串匹配另一个字符串所需的一个字符变化的数量。基本上，它是你能打多少个错别字而仍然匹配字符串。

*示例*:您的目标匹配数据是一个值为“ajsharp”的字段。当`fuzziness`参数设置为 1 时，您可以输入“jjsharp ”,它将匹配“ajsharp”。但是如果你输入“jssharp”它不会匹配，除非你把`fuzziness`增加到 2。

## `match_phrase` -用于匹配精确短语

*   [文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query-phrase.html)

**何时使用:**如果您需要特定的整个短语搜索，或者希望为您的用户启用此功能。

假设我们有两个被索引的 twitter 个人资料。文档 A 在简历中有“联合创始人”一词，文档 B 只是有“创始人”一词。

如果使用 match_phrase 搜索“founder”，两个文档都会匹配。
如果使用 match_phrase 搜索“联合创始人”，只会匹配到文档 A。然而，如果您使用`match`完成这个查询，文档 A 和文档 B 都将匹配查询“联合创始人”

## `match_phrase_prefix`——可怜人的自动完成

*   [文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query-phrase-prefix.html)

**何时使用**:只有你需要匹配单个字段。对于许多用例来说`multi_match`感觉*远*更有用。

从 ES 文档中:

> 考虑查询字符串 quick brown f。该查询的工作原理是用 quick 和 brown 创建一个短语 query(即，术语 quick 必须存在，后面必须跟有术语 brown)。然后，它查看已排序的术语词典，查找以 f 开头的前 50 个术语，并将这些术语添加到短语查询中。

*   可以用[完成建议器](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html)增强/改进

## `multi_match` -多场比赛🎉🎉🎉

允许您在多个字段中搜索相同的字符串。

**何时使用**:在很多情况下，如果你正在做任何自动完成的事情，这可能是你所需要的(注意:可能有很多情况下这是很好的，但是我写这篇文章时的主要情况是自动完成，所以🤷🏻‍♂️).

多匹配查询可以有一种类型:

*   `best_fields`。默认。匹配任何字段，但使用最佳字段中的 _score。[文件](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-multi-match-query.html#type-best-fields)
*   `most_fields` -匹配任何字段并组合分数
*   `cross_fields` -用相同的分析仪将字段视为一个大字段。
*   `phrase` -使用`match_phrase`
*   🎉🎉🎉`phrase_prefix` -在每个字段上使用`match_phrase_prefix`并合并 _score。允许多字段自动完成💥💥💥

**字段增强**
字段可以通过使用`^`后跟数字来增强。假设 twitter 配置文件具有 screen_name 和 name 字段。如果你做这样的查询，screen_name 的重要性将是排名中 name 字段的三倍:

```
{
  "query": {
    "multi_match" : {
      "query" : “ajsharp”,
      "fields" : [ “screen_name^3", “name” ] 
    }
  }
} 
```

* * *

☝️ ***一定要看看 [Sharesecret](https://www.sharesecret.co) ，它让安全共享敏感数据变得很容易。***