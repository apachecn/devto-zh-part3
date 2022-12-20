# 希望在应用中使用混合字符和通配符搜索？

> 原文：<https://dev.to/groupdocs/looking-to-utilize-blended-characters-and-wildcard-search-in-your-apps-lc1>

在不同的开发平台上构建智能、功能丰富且可靠的应用程序来搜索各种文件类型绝非易事。每个终端用户都有自己独特的需求，这本身就给应用程序开发人员带来了新的挑战。

说到平台。NET 是全球程序员开发无数不同类型的软件应用程序的共同狩猎场。例如，用于索引和搜索从 Microsoft Office 和 OpenOffice 文件、ZIP 存档、电子邮件或 PDF 文档中获取的数据的商业应用程序。索引过程让您收集，分析和存储数据，更快，更准确的搜索。

在给定的场景中，您可能需要使用多个索引来索引多组检索到的信息。因此，你可以自己从头开始编写一个程序，或者，你可以使用一个可靠的 API 来帮助你将它的特性整合到你计划创建的应用程序中。GroupDocs。搜索。NET 是一个文本搜索 API，它为您提供了在。网络搜索和索引应用熟练。

这其中的一个显著特征。NET API 是在你的应用程序中使用混合字符。这个特性的想法是，这样的字符可以帮助您减少搜索项，当索引时，混合字符同时被解释为有效的字母和分隔符。例如，考虑将连字符(-)标记为混合字符，现在当您索引一个术语，如“绿色苹果”时，它将为您节省三个搜索术语:“绿色”、“苹果”和“绿色苹果”。

以下代码片段演示了如何使用混合字符:

`string indexFolder = @"c:\MyIndex";
string documentFolder = @"c:\MyDocuments";
// Creating index
Index index = new Index(indexFolder);
// Marking hyphen as blended character
index.Dictionaries.Alphabet.SetRange(new char[] { '-' }, CharacterType.Blended);
// Adding documents to index
index.AddToIndex(documentFolder);
// Searching for word 'silver-gray'
SearchResults results = index.Search("silver-gray");`

另一个突出的特性是搜索包含通配符的单词；支持两种形式，单个通配符任意字符或一系列字符。

要检查此功能如何工作，请参考以下代码片段:

`string indexFolder = @"c:\MyIndex";
string documentFolder = @"c:\MyDocuments";
// Creating index
Index index = new Index(indexFolder);
// Adding documents to index
index.AddToIndex(documentFolder);
// Searching for words 'affect' or 'effect' in a one document with
// 'principal', 'principle', 'principles', or 'principally'
SearchResults results1 = index.Search("?ffect & princip?(2~4)");
// Searching with a single query for phrases 'assure equal opportunities',
// 'ensure equal opportunities', and 'sure equal opportunities'
SearchResults results2 = index.Search("\"?(0~2)sure equal opportunities\"");`

了解更多-[http://bit.ly/2AT9qXi](http://bit.ly/2AT9qXi)