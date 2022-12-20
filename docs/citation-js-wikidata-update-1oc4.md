# Citation.js:维基数据更新

> 原文：<https://dev.to/citation-js/citation-js-wikidata-update-1oc4>

新的更新`v0.4.4`，包含了一些[维基数据](https://wikidata.org)的改进([提交](%5Bhttps://github.com/citation-js/citation-js/commit/01be9362a9106b886fc6d8f37d8a4d53ecc49d22%5D(https://github.com/citation-js/citation-js/commit/01be9362a9106b886fc6d8f37d8a4d53ecc49d22))):

*   22 个新映射
*   2 个固定映射(`ISSN`不起作用，并且`publisher-place`被映射到错误的东西)
*   2 个改进的映射(章节的`container-title`和更多的`URL`映射)
*   1 移除标测(`genre`与[预期用途](%5Bhttps://discourse.citationstyles.org/t/inconsistencies-with-genre-and-medium/1475%5D(https://discourse.citationstyles.org/t/inconsistencies-with-genre-and-medium/1475))不一致，尽管其符合规范)

因为大多数映射需要额外的资源(`recipient`必须获取人员，`review-*`必须获取评论主题，`original-publisher-place`必须获取作品原始版本的出版商的国家和位置)，这将导致对 API 的更多请求。因此，有一个新的解析器是一件好事，它可以一次为所有请求的工作预取这样的信息。

这必须分层次进行；如果你没有位置，你就不能获取国家，位置需要出版商，要找到出版商，你必须已经有了作品。但是，暂时忽略每项申请 50 件物品的上限，现在的申请数量仅基于这些级别(最多 5 件)，而不是需要额外申请的申请数量。

例如，项目 [`Q21972834`](https://www.wikidata.org/wiki/Q21972834) ( *从全基因组鸟枪法测序数据*中组装 20 Gb 白云杉基因组)需要:

*   **6 个请求`v0.4.0-rc.2`中的**，在对属性的请求被分组之前(因此它将为每个作者发出一个请求)
*   **3 个请求`v0.4.3`中的**:一个针对作品，一个针对作者，一个针对期刊
*   **2 个请求`v0.4.4`中的**，这包括一堆新的映射，在以前的版本中总共需要 5 个请求

对所有作品的请求进行分组还有额外的好处，即不必重复获取相同的作者和期刊信息——这在书目中很常见。例如，目前包含 74 个条目的 [*常规化学信息学与化学开发工具包*](https://egonw.github.io/cdkbook) 的参考书目，在新版本中需要 11 个请求。对于同一个参考书目，以前的版本会提出至少 150 个请求——每个条目一个给所有作者，一个给期刊——可能更多。

```
[core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q24599948%7CQ24650571%7CQ25713029%7CQ27061829%7CQ27062363%7CQ27062596%7CQ27065423%7CQ27093381%7CQ27134682%7CQ27134746%7CQ27134827%7CQ27162658%7CQ27211680%7CQ27499209%7CQ27656255%7CQ27783585%7CQ27783587%7CQ27902272%7CQ28090714%7CQ28133283%7CQ28186592%7CQ28837846%7CQ28837922%7CQ28837925%7CQ28837939%7CQ28837943%7CQ28837947%7CQ28842810%7CQ28842968%7CQ28843132%7CQ29039683%7CQ29042322%7CQ29616639%7CQ30149558%7CQ30853915%7CQ31127242%7CQ33874102%7CQ34160151%7CQ34206190%7CQ36662828%7CQ37988904%7CQ39811432%7CQ42704791%7CQ47543807%7CQ47632144%7CQ54062338%7CQ55880270%7CQ55934414%7CQ55954394%7CQ56112883&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q56170978%7CQ56454405%7CQ57836257%7CQ59771351%7CQ60167226%7CQ60167327%7CQ60167615%7CQ60167690%7CQ61463648%7CQ61649587%7CQ61779181%7CQ61779373%7CQ61779901%7CQ61779905%7CQ61779940%7CQ61780124%7CQ62926016%7CQ62926155%7CQ62927888%7CQ62968825%7CQ62969352%7CQ62969354%7CQ63367539%7CQ63367548&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q135122%7CQ1223539%7CQ59196164%7CQ1860%7CQ8513%7CQ766195%7CQ7441%7CQ28946414%7CQ50332566%7CQ46330054%7CQ57218960%7CQ57218835%7CQ55965812%7CQ43370919%7CQ37391332%7CQ57677801%7CQ60446154%7CQ60447576%7CQ60449513%7CQ29946263%7CQ60465926%7CQ60890297%7CQ20895241%7CQ910067%7CQ3007982%7CQ52113739%7CQ5111731%7CQ29405902%7CQ47473872%7CQ121182%7CQ128570%7CQ910164%7CQ2383032%7CQ1130645%7CQ908710%7CQ5727848%7CQ27065426%7CQ28946652%7CQ42783959%7CQ4420286%7CQ749647%7CQ309823%7CQ28540892%7CQ29052381%7CQ29052386%7CQ4914910%7CQ12149006%7CQ853614%7CQ1418791%7CQ50731867&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q56007851%7CQ5195068%7CQ11351%7CQ75%7CQ151332%7CQ3002926%7CQ27061944%7CQ27134800%7CQ6294930%7CQ27061853%7CQ28540435%7CQ28854723%7CQ766383%7CQ1425625%7CQ28540616%7CQ55406442%7CQ483666%7CQ11173%7CQ39972290%7CQ1069211%7CQ36534%7CQ27211732%7CQ251%7CQ1768406%7CQ1689854%7CQ30046697%7CQ55406542%7CQ5227350%7CQ38372872%7CQ38373802%7CQ3841253%7CQ55213915%7CQ7440973%7CQ30045595%7CQ451553%7CQ466769%7CQ203250%7CQ54837%7CQ3355939%7CQ40023319%7CQ26842658%7CQ109081%7CQ82264%7CQ898902%7CQ27711423%7CQ1767639%7CQ7209103%7CQ27768873%7CQ900316%7CQ48803&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q1884753%7CQ15064016%7CQ895901%7CQ2166958%7CQ907701%7CQ309%7CQ28796322%7CQ19845641%7CQ27061849%7CQ28923506%7CQ30046701%7CQ28865170%7CQ43370334%7CQ29387575%7CQ50983316%7CQ7395247%7CQ192864%7CQ336658%7CQ58409692%7CQ56421913%7CQ55182163%7CQ55182165%7CQ56670283%7CQ925779%7CQ50731930%7CQ909510%7CQ381009%7CQ38173%7CQ30046335%7CQ43370883%7CQ3705921%7CQ1154615%7CQ2334061%7CQ1988917%7CQ898967%7CQ2425378%7CQ10354104%7CQ47 480%7CQ1709878%7CQ900502&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q1120519%7CQ1860%7CQ113337%7CQ1767639%7CQ27134800%7CQ6294930%7CQ251%7CQ5776092%7CQ56421877%7CQ463360%7CQ109081%7CQ50731930%7CQ176916%7CQ26707540%7CQ30046697%7CQ28925563%7CQ1122491%7CQ3186908%7CQ969707%7CQ1948400%7CQ192864%7CQ898902%7CQ2835897%7CQ3007982%7CQ46155617%7CQ905549%7CQ485223%7CQ900502%7CQ15766522&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q7050%7CQ32%7CQ64%7CQ225471&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q47501431&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q33467704&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q55&format=json&languages=en {} [core] GET https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q183%7CQ145&format=json&languages=en {} 
```

然而，我*确实*注意到了比我预期的稍微多一点的带有少量条目的请求。我会调查的。

### 维基数据中的会议论文

在最近的更新中添加的部分映射是`event-*`属性，即“相关事件的名称(例如引用会议论文时的会议名称)”([规格](https://docs.citationstyles.org/en/master/specification.html#appendix-iv-variables))。事实证明，在维基数据中找出这些是如何建模的有点困难，因为大多数 [`Q23927052`(会议论文)](https://www.wikidata.org/wiki/Q23927052)的实例都是以`book`类型发表在《会议录》上的。获取一些数字([查询](https://w.wiki/4JL)):

| 类型 | 类型标签 | 项目 |
| --- | --- | --- |
| [Q571](http://www.wikidata.org/entity/Q571) | 书 | Seven hundred and fifteen |
| [Q5633421](http://www.wikidata.org/entity/Q5633421) | 科学杂志 | Six hundred and seventy-six |
| [Q1143604](http://www.wikidata.org/entity/Q1143604) | 诉讼 | Three hundred and fifteen |
| [Q16024164](http://www.wikidata.org/entity/Q16024164) | 医学期刊 | Fifty-three |
| [Q23927052](http://www.wikidata.org/entity/Q23927052) | 会议论文 | Forty-eight |
| [Q1002697](http://www.wikidata.org/entity/Q1002697) | 期刊文献 | Thirty-two |
| [Q41298](http://www.wikidata.org/entity/Q41298) | 杂志 | Twenty-nine |
| 其他的 | 其他的 | Sixty-one |

48 篇会议论文发表在会议论文上？这似乎不对。但也许他们只是有错误的类型，但仍然链接到事件？嗯不，不是真的([查询](https://w.wiki/4JN))。

| 有事件 | 项目 |
| --- | --- |
| 错误的 | One thousand nine hundred and fifty-two |
| 真实的 | six |

幸运的是，信息没有丢失:通常，标签包含事件的位置和日期信息，尽管提取这些信息可能是一项乏味的任务。然而，也许有很多正确的程序，但链接到它的文章只是类型错误( [query](https://w.wiki/4JH) )？

| 类型 | 类型标签 | 项目 |
| --- | --- | --- |
| [Q13442814](http://www.wikidata.org/entity/Q13442814) | 学术文章 | Three thousand five hundred and thirteen |
| [Q23927052](http://www.wikidata.org/entity/Q23927052) | 会议论文 | Three hundred and fifteen |
| [Q3331189](http://www.wikidata.org/entity/Q3331189) | 版本、版本或翻译 | Eleven |
| [Q1143604](http://www.wikidata.org/entity/Q1143604) | 诉讼 | nine |
| [Q10885494](http://www.wikidata.org/entity/Q10885494) | 科学会议论文 | seven |
| [Q1980247](http://www.wikidata.org/entity/Q1980247) | 回 | six |
| [Q191067](http://www.wikidata.org/entity/Q191067) | 文章 | five |
| [Q18918145](http://www.wikidata.org/entity/Q18918145) | 学术期刊文章 | four |
| [Q333291](http://www.wikidata.org/entity/Q333291) | 摘要 | three |
| 其他的 | 其他的 | Ten |

事实似乎是这样的:大多数发表在《论文集》上的作品都被标记为学术文章的实例，只有 9 篇被标记为两者都是。并且:相对而言，许多事件都与它们相关联([查询](https://w.wiki/4JP))。

| 有事件 | 项目 |
| --- | --- |
| 真实的 | Two thousand six hundred and ninety-two |
| 错误的 | One thousand one hundred and eighty-four |

那更好。我会考虑和 WikiCite 一起工作来完成剩下的部分。