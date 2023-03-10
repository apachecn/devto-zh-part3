# 在 Java 应用程序中实现通配符搜索功能

> 原文：<https://dev.to/groupdocs/implementing-wildcard-search-functionality-in-your-java-applications-3jc8>

如今，索引是一个与搜索引擎同义的术语，然而，根据定义，它指的是根据特定的模式组织数据。换句话说，这是一个让数据和信息更容易获取的过程。

在信息搜索或检索过程中，索引中的数据安排可以节省您的时间和精力。从长远来看，考虑一本包含数百页的关于空间科学的书。如果它不包括索引，每次你想查看想要的信息时，你就必须搜索整本书。这将是一个完全不同的场景，这本书有一个索引，你可以打开确切的页面，其中包含你正在寻找的信息。

作为一名程序员，您可能会遇到在某些类型的文档中进行数据索引的业务需求。这需要创建一个复杂的模式，不仅允许您无缝地索引信息，还允许您在需要时执行搜索操作。除此之外，还需要在 Java 平台上执行所有这些操作，您将需要一个 API 产品:

*   支持多格式文档
*   创建多个指数的能力
*   几种类型的搜索能力

GroupDocs。Search for Java 是一个独特的索引 API，通过提供完善的功能集和灵活的结构来满足上述要求。它允许 Java 应用程序开发人员创建和管理多个索引，并使用各种搜索查询，如简单、布尔、正则表达式(regex)或模糊搜索。

您可以在这个 Java API 中使用混合字符，这有助于您在索引时将连字符等字符用作有效的字母和分隔符。通配符搜索选项是这个 API 的另一个非常有用的特性。有两种形式的通配符可用，单个任意字符或一系列通配符任意字符。

以下代码片段显示了如何使用文本查询执行通配符搜索:
`// Creating index
Index index = new Index(Utilities.INDEX_PATH,true);
// Adding documents to index
index.addToIndex(Utilities.DOCUMENTS_PATH,true);
// Searching for words 'affect' or 'effect' in a one document with 'principal', 'principle', 'principles', or 'principally'
SearchResults results1 = index.search("?ffect & princip?(2~4)");
// Searching with a single query for phrases 'assure equal opportunities', 'ensure equal opportunities', and 'sure equal opportunities'
SearchResults results2 = index.search("\"?(0~2)sure equal opportunities\"");`

要使用 object 搜索通配符，请参考下面的代码片段:
`// Creating index
Index index = new Index(Utilities.INDEX_PATH,true);
// Adding documents to index
index.addToIndex(Utilities.DOCUMENTS_PATH, true);
// Constructing query 1
// Word 1 in the query is a pattern '?ffect' for wildcard search
WordPattern pattert11 = new WordPattern();
pattert11.appendOneCharacterWildcard();
pattert11.appendString("ffect");
SearchQuery subquery11 = SearchQuery.createWordPatternQuery(pattert11);
// Word 2 in the query is a pattern 'princip?(2~4)' for wildcard search
WordPattern pattert12 = new WordPattern();
pattert12.appendString("princip");
pattert12.appendWildcard(2, 4);
SearchQuery subquery12 = SearchQuery.createWordPatternQuery(pattert12);
// Creating boolean search query
SearchQuery query1 = SearchQuery.createAndQuery(subquery11, subquery12);
// Searching with query 1
SearchResults results1 = index.search(query1, new SearchParameters());
// Constructing query 2
// Word 1 in the phrase is a pattern '?(0~2)sure' for wildcard search
WordPattern pattert21 = new WordPattern();
pattert21.appendWildcard(0, 2);
pattert21.appendString("sure");
SearchQuery subquery21 = SearchQuery.createWordPatternQuery(pattert21);
// Word 2 in the phrase is searched with different word forms ('equal', 'equals', 'equally', etc.)
SearchQuery subquery22 = SearchQuery.createWordQuery("equal");
subquery22.setSearchParameters(new SearchParameters());
subquery22.getSearchParameters().setUseWordFormsSearch(true);
// Word 3 in the phrase is searched with maximum 2 differences of fuzzy search
SearchQuery subquery23 = SearchQuery.createWordQuery("opportunities");
subquery23.setSearchParameters(new SearchParameters());
subquery23.getSearchParameters().getFuzzySearch().setEnabled(true);
subquery23.getSearchParameters().getFuzzySearch().setFuzzyAlgorithm(new TableDiscreteFunction(2));
// Creating phrase search query
SearchQuery query2 = SearchQuery.createPhraseSearchQuery(subquery21, subquery22, subquery23);
// Searching with query 2
SearchResults results2 = index.search(query2, new SearchParameters());`

今天就亲自检查所有可用的功能-[http://bit.ly/2TmhZRO](http://bit.ly/2TmhZRO)