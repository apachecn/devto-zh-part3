# 使用 Python 的 Twitter 抓取、文本挖掘和情感分析

> 原文：<https://dev.to/erikafu/twitter-scraping-text-mining-and-sentiment-analysis-using-python-9g5>

我不太喜欢唐纳德·特朗普。严格来说，我一点也不喜欢他。然而，他有这种魅力的轰动效应，占据了大多数报纸和社交媒体的所有时间。人们对他的态度是戏剧性的和双边的。他的描述性文字或高度肯定或高度否定，是文本挖掘和情感分析的一些完美素材。本次研讨会的目标是使用一个网站抓取器来阅读和抓取关于唐纳德·特朗普的推文。然后，我们将使用文本挖掘和可视化技术的组合来分析关于唐纳德·特朗普的公众声音。

**如果您不知道如何在社交媒体上抓取内容/评论，您应该继续阅读:**
。
OR/AND IF 您了解 Python，但不知道如何将其用于情感分析。
这个车间很容易仿效。即使您对编程一无所知，您也应该在阅读本文时感到舒服。请随意复制代码并自己尝试。如果您是初学者，我建议您先试用您的代码，然后再与本研讨会中的代码进行比较。

**Twitter 抓取:**
先说 web 抓取，我需要一个有效的 web 抓取工具来替我做所有枯燥的工作。任何网络刮刀工具都可以工作。我推荐 [**Octoparse**](https://www.octoparse.com/) ，因为它是免费的，没有页数限制。

我从它的官方网站下载了它，并按照说明完成了注册。我登录后，打开了他们内置的 Twitter 模板。

[![Alt text of image](img/c154f22cdf665cca5f8cbe435d7cec38.png)](https://cdn-images-1.medium.com/max/800/0*xDjAQtdCtbBSJO4w)

**八分抓取模板**
模板上的抓取规则预先设置了数据提取字段，包括**名称、ID、内容、评论等。**

[![Alt text of image](img/434dd21aa95834ac33b5cda5c4ffb3c0.png)](https://cdn-images-1.medium.com/max/800/0*cT3gs8KybBZemLsU) 
我在外围输入“唐纳德·特朗普”鱼贯告诉爬虫这个关键词。就像看起来那么简单，我收到了大约 1 万条推文。你可以尽可能多的抓取推文。还有一些其他的方式来抓取数据，可能你能得到比我更好的结果。欢迎与我分享你的创新爬行经验，我永远是一个充满激情的学习者:)

获得 tweets 后，将数据导出为文本文件，将文件命名为“data.txt”。

**文本挖掘:**
在开始之前，确保你的电脑上安装了 Python 和一个文本编辑器。我用的是 [Python 2.7](https://www.python.org/download/releases/2.7/) 和 [Notepad++](https://notepad-plus-plus.org/download/v7.6.6.html) 。

然后我们用两个观点词表对抓取的推文进行分析。你可以从[这里](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fjeffreybreen%2Ftwitter-sentiment-analysis-tutorial-201107%2Ftree%2Fmaster%2Fdata%2Fopinion-lexicon-English)下载。这两个列表包含了来自[调查研究](https://www.cs.uic.edu/~liub/FBS/sentiment-analysis.html)的胡敏清和刘冰总结的关于社交媒体中呈现意见词的正面词和负面词(情感词)。

这里的想法是从列表中取出每个观点词，返回到推文中，并统计每个观点词在推文中的出现频率。因此，我们在 tweets 和 count 中收集相应的意见词。

首先，我用两个下载的单词列表在第 5 行和第 13 行创建了一个正面和负面列表。它们存储从文本文件中解析出的所有单词。

然后，我用下面的代码处理文本和数据，去掉所有的标点符号和数字。

因此，数据仅由标记化的单词组成，这使得分析更容易。(这是我发现的关于数据科学中文本预处理有用的[博客](https://medium.com/@datamonsters/text-preprocessing-in-python-steps-tools-and-examples-bf025f872908)。)

然后，创建三个字典:word_count_dict、word_count_positive 和 word_count_negative。

接下来，我定义了每个字典。如果数据中存在意见词，则通过将 word_count_dict 值增加“1”来对其进行计数。

在单词计数之后，我们需要决定一个单词听起来是积极的还是消极的。如果是正词，word_count_positive 将其值增加“1”，否则正词典保持相同值。word_count_negative 分别增加其值或保持相同的值。如果这个单词既没有出现在肯定列表中，也没有出现在否定列表中，那么它就通过了。

完整版本的代码可以在这里下载[https://gist . github . com/octo parse/fd9e 0006794754 edfbdaea 86 de 5b 1 a 51](https://gist.github.com/octoparse/fd9e0006794754edfbdaea86de5b1a51)

**极性:正负**
[![Alt text of image](img/0dcf651e972647e38504cba5cc6cd52c.png)](https://cdn-images-1.medium.com/max/800/0*paSV0NDv-t8_C6ZL)

5352 个否定词和 3894 个肯定词
，如图所示。正面词的使用是单方面的。只使用了 404 种肯定词。
[![Alt text of image](img/0fa6be1170eb23cf38514d267f063c74.png)](https://cdn-images-1.medium.com/max/800/0*2u9tvW3hbqOXM3ly)

最常用的词是，比如，“喜欢”、“很棒”、“对”。大多数单词的选择是基本的和口语化的，比如“哇”和“酷”，而否定词的使用则更加多样化。共有 809 种否定词，其中大部分是正式的和高级的。最常用的是"**非法的**、" "**谎言**、"**种族主义者**"其他高级词如“**不良**”、“**炎性**”、“**伪君子**”也有。
T13![Alt text of image](img/92230e215d2ce5237be602df0189ebdf.png)T15】

用词清楚地表明，支持者的教育水平低于反对者。显然，唐纳德·特朗普在推特用户中并不那么受欢迎。

**总结:**
在这篇文章中，我们讨论了如何使用 Octoparse 抓取 Twitter 上的推文。我们还讨论了使用 python 进行文本挖掘和情感分析。

这项研究有一些限制。我删了 15K 条推特。然而，在搜集的数据中，有 5000 条推文要么没有文本内容，要么没有显示任何意见词。因此，情绪分析是有争议的。此外，本文中的分析只关注两极分化的观点(无论是积极的还是消极的)。细粒度的情感分析应该更加精确到不同的程度(非常正面、正面、中性、负面、非常负面)。

**为什么“违法”？**
最后，我想分享一些关于结果的想法。“非法”一词是与唐纳德·特朗普相关的最负面的词。这个词排名第一并不奇怪，因为唐纳德·特朗普自上任以来一直致力于关注移民问题。然而，我对人们开始滥用这个词感到惊讶。我找出了关于这个词的推文，大多数都是“非法移民”和“非法外国人”这让我想到，从什么时候开始“无证”等同于“非法”？

最后，我想引用埃利·威塞尔的话，“你们这些所谓的非法外国人必须知道，没有人是非法的。这是一个矛盾的说法。人可以漂亮或更漂亮，可以胖或瘦，可以对或错，但不违法？一个人怎么可能违法？”

**资源:**

[https://medium . com/@ data monsters/text-预处理-in-python-steps-tools-and-examples-BF 025 f 872908](https://medium.com/@datamonsters/text-preprocessing-in-python-steps-tools-and-examples-bf025f872908)
[http://nohumanbeingisillegal.com/Home.html](http://nohumanbeingisillegal.com/Home.html)
[https://github . com/Jeffrey breen/Twitter-情操-分析-教程-2011107/blob/master/data/opinion-lexicon-English/positive-words . txt](https://github.com/jeffreybreen/twitter-sentiment-analysis-tutorial-201107/blob/master/data/opinion-lexicon-English/positive-words.txt)

你会给我一些可爱的掌声，让我高兴一天。你的支持对我很重要。我喜欢写和分享关于网络抓取的东西。不要犹豫，在评论中分享你的想法。