# 静悄悄的革命:JSON 如何取代 XML

> 原文：<https://dev.to/bnevilleoneill/the-quiet-revolution-how-json-displaced-xml-1pc6>

[![](img/b9e1f91cf46f7af69b35b0ecd355a980.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3xULMu3U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/770/1%2AL3a36IrkMQJmPGuOkiEaQg.jpeg)

我的一位计算机科学教授曾经说过，“为了让我理解你的代码，给我看你的数据。”数据设计是代码设计的核心。它可以塑造代码的特征。架构决策可以对程序执行期间使用的数据量和数据类型进行评估。

虽然在软件应用程序中从关系数据库甚至是包含数据列的平面文件(CSV 或 TSV)中读取数据并不罕见，但通常需要更优雅的结构来表达数据之间更复杂的关系。这就是 XML 和 JSON 被广泛使用的地方。XML 已经使用了很多年，但是 JSON 逐渐成为许多应用程序选择的数据格式。

XML 和 JSON 都有一些反映计算机应用程序中数据组织方式的基本特性:

*   具有属性的数据对象
*   表达数据对象之间从属关系的层次结构
*   数组在一个地方收集尽可能多的相似数据对象

**带属性的数据**是计算机科学中的一个基本概念。这是面向对象编程的核心特性，在 C 和 C++有结构之前，Lisp 有关联列表和属性。属性捕捉数据的特征。一个代表客户的数据对象会有名字、姓氏、年龄、性别等细节。具有属性的数据对象也可以表示字典，即从一组数据值映射到另一组数据值的结构(就像月份名称到月份号的映射，“一月”是 1，“二月”是 2，等等)。这是一种在软件中编码一些智能的强大方法，定义了反映数据之间含义的关联。

**层级**是表达相关对象之间关系的常用方式。客户可能有一个地址，该地址又有一些属性，如街道名称、城市、国家和邮政编码。层次结构可能还涉及分组，比如客户未完成的产品订单列表。

**数组**提供了一种在一个地方收集多个数据实例的方法，提供了在一个简单的代码循环结构中处理数据的机会。同一个编程循环可以处理任意数量的数据，无论是 500 还是 5，000，000，并且是创建能够灵活处理任意大量数据的强大代码的关键。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### XML 的诞生

20 世纪 90 年代中期，软件开发人员开始使用 [XML](https://en.wikipedia.org/wiki/XML) 来定义结构化数据。HTML 已经被非常成功地用来标记 web 文档的元素，以指定它们的外观。XML 使用非常相似的标记符号来指定数据的各个部分及其重要性。HTML 是为网页浏览器阅读和解释而设计的。XML 被设计成主要由应用软件读取。

下面是一个 XML 语法的例子，表示关于客户及其最近订单的一些数据，展示了属性、层次结构和数组:

[https://medium . com/media/447 fc 229124 b 2660 ebb 12254 FDC 696 e 1/href](https://medium.com/media/447fc229124b2660ebb12254fdc696e1/href)

(为了可读性，这里的例子被很好地格式化和缩进。在实际的应用程序中，换行符和缩进很可能会被去掉——即使人类看不到，计算机仍然可以阅读)

XML 作为在所谓的“多层”应用程序中在客户端和服务器端之间交换数据的一种方式变得非常流行，并且还通常用于定义许多应用程序的配置文件的格式。开发了软件标准和工具来指定、验证和操作 XML 结构的数据。 [DTDs(数据类型定义)](https://en.wikipedia.org/wiki/Document_type_definition)以及后来的 [XSchema](https://en.wikipedia.org/wiki/XML_schema) 来表达 XML 数据的结构； [XSLT](https://en.wikipedia.org/wiki/XSLT) 将 XML 数据从一种格式转换成另一种格式——每种格式本身都以 XML 格式编码(在 dtd 中类似于 XML)。

但是 XML 的流行也伴随着 B2B 应用的增长。XML 开始被用来在大大小小的合作公司之间传递业务关键数据，像 Aruba 和 T2 Commerce One 这样的初创公司在这个时候出现，为数据交换提供平台和工具包。SOAP(“简单对象访问协议”)是作为基于 XML 的交换协议而引入的:一个 XML 报头的公共“信封”,它提供了一种指定寻址/路由和安全性的方法，以及“有效负载”部分，它承载了要从一台计算机发送到另一台计算机的特定于应用程序的数据。其他标准是在 B2B 应用的“电子数据交换”(EDI)的大框架下开发的。

### XML——好的、坏的、丑陋的

XML 是一个强大的数据处理和交换标准。但是它有一些怪癖和局限性。

它可能非常冗长。XML 元素开始处的前导标记定义了机器处理的内容，以便人们能够阅读。当您将“Customer”视为 XML 元素的开头时，您就知道该元素包含哪种数据。尾随标签稍微提高了人们的可读性，但并没有真正增加机器的可读性。取消 XML 元素的结束标记，采用更简单的方式来终止内容，可以显著减少数据的大小。

此外，XML 中没有数组元素的显式表示。打算作为一个组来处理的相似对象的集合被简单地放在一个公共元素下。但是在 XML 数据中没有明确指出这种意图。可以在 DTD 或 XSchema 中创建一个规范来定义这一点，从阅读处理数据的代码中可以清楚地看到，代码正在循环处理重复的 XML 元素。

但是 XML 没有提供数据数组的可视指示器。可以通过使用包装元素来创建这样的指示符(比如一组“<订单>”元素周围的“<订单>”元素)，但是这种语法在 XML 中不是必需的。

XML 确实支持命名空间，命名空间是元素名称的前缀，表示它属于某个相关标记组，很可能是由一个单独的组织发起的，并由一个不同的 XML 模式管理。它对计算机的组织和验证很有用(特别是对数据交换的各个部分进行划分/分类:SOAP 信封和有效载荷等)，但是增加了 XML 解析的复杂性，也给读者带来了视觉混乱。

接下来是软件工程中一个经典的争论话题(就在那里，用“同一行或下一行的花括号”):[属性或元素应该用于数据对象的属性](https://www.ibm.com/developerworks/xml/library/x-eleatt/index.html)？XML 将这种选择留给了实现者。关于客户对象的详细信息同样可以使用 XML 属性来指定:

[https://medium . com/media/0d 2248 FAA 44 c 67 D5 bbdf 95220 c 978 ba 3/href](https://medium.com/media/0d2248faa44c67d5bbdf95220c978ba3/href)

…或者使用 XML 数据对象的子元素:

[https://medium . com/media/d8ca 6500 fdb 06505 cdcd 87 d0f 8161 E0 e/href](https://medium.com/media/d8ca6500fdb06505cdcd87d0f8161e0e/href)

元素的属性名必须是唯一的，不能超过一个。但是在任何给定的元素下，可以有多个具有相同标记名的子元素。

子元素有一个隐含的顺序，可以被生产和消费代码视为有意义的(没有任何视觉提示)。属性没有明确的顺序。

有一种观点认为属性应该表达与 XML 元素的“是-是”关系，而子元素表达的是“有-是”关系，但是在很多情况下，这个决定是一个灰色区域。

### JSON 一炮走红

在 21 世纪初，提出了一种替代格式:JavaScript 对象表示法，也称为 JSON。作为 ECMAScript 规范早期版本的一部分，JSON 得到了道格拉斯·克洛克福特的支持(《Javascript:好的部分》的作者)。2006 年，克罗克福德创建了 json.org 的[网站](http://www.json.org/)来赞美 JSON 的优点，称 JSON 是“……一种轻量级的数据交换格式。对人类来说，读和写很容易。机器很容易解析生成。它基于 JavaScript 编程语言的一个子集”。

下面是一个相同的客户数据示例，格式为 JSON:

[https://medium . com/media/4a 5 DC 5 f 6 FD 8 eccd 18 FB 355 b 70 bb 8 b 8 ff/href](https://medium.com/media/4a5dc5f6fd8eccd18fb355b70bb8b8ff/href)

JSON 显式地表示对象(字典)和数组。它本质上是一种字典类型的数据表示。XML 层次结构是用 XML 中的嵌套元素表示的，而在 JSON 中，它是用父对象的一个属性(或者用 Javascript 术语来说，一个属性)来表示的，父对象的值是子对象(注意上面例子中的“address”或“orders”属性)。数组也是用方括号显式表示的，可以保存基本类型，如字符串或数字以及对象。

与 XML 格式相比，JSON 大大简化了事情。JSON 中唯一可以表达的关联是属性。层次结构由嵌套的花括号表示，其中每个花括号括起来的对象都与其父对象的一个属性相关联。在层次结构的每一层都没有终止名称或标签，只有一个右花括号，这使得 JSON 比 XML 更简单、更简洁地编码数据集合。

这与 Javascript 语言有着密切的联系:JSON 本质上是 Javascript 对象文字的表示，对象文字是 Javascript 的核心特性之一。

JSON 当然是作为 Javascript 发展的一部分成长起来的，成为今天卓越的软件开发语言。随着 Angular 和 React 等越来越复杂的 Javascript 框架的出现(还有 grunt、gulp、web pack……这个列表还在继续),同构开发的概念已经深入人心:到处都在使用 Javascript。有几本书是关于[“MEAN”开发](https://www.mongodb.com/blog/post/the-modern-application-stack-part-1-introducing-the-mean-stack)的，使用 MongoDB、Express、Angular 和 Node 作为 web 应用程序的所有层(用您选择的前端框架代替 Angular)。JSON 是服务器端和前端之间数据交换格式的自然选择。这是在 MongoDB 中存储数据的自然格式(MongoDB 是用 C++实现的，但以类似 JSON 的格式存储数据，这种格式被称为 BSON，是 JSON 的二进制序列化)。MongoDB 查询中的条件使用 Javascript 对象文字表示，Javascript 代码可用于解释 MongoDB 查询的 JSON 结果。

解析 XML 需要使用 API——某种用编程语言编写的库。JSON 也是如此，除了在 Javascript 中:JSON.parse()函数(从 ES6 开始支持)将 JSON 从字符串形式转换成本地 Javascript 对象、数组和散列。一旦 JSON 被解析，它就可以作为常规的 Javascript 数据结构被遍历。这是 JSON 让 Javascript 中的同构编程大获成功的另一种方式！其他软件开发语言(Python、PHP、Ruby、Java)确实提供了现成的 JSON 解析支持，使 JSON 成为在用不同语言编写的应用程序之间交换数据的一种方式。

### 回到未来:JSON 数据表示的先驱

JSON 数据看起来如此像 Javascript 对象字面语法可能不是偶然的。

Javascript 的创始人布伦丹·艾希(Brendan Eich)从 Javascript 的语言[计划](https://en.wikipedia.org/wiki/Scheme_%28programming_language%29)和[自我](https://en.wikipedia.org/wiki/Self_(programming_language))中借鉴了一些想法。Scheme 是 [Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language)) 的一种方言，Lisp 的语法是“[同音](https://en.wikipedia.org/wiki/Homoiconicity)”——代码和数据以完全相同的方式表示，使用非常简单的嵌套括号语法。Lisp 中的所有代码和数据都是一个列表(就像一个数组)。字典可以用嵌套列表来表示。

下面是 Lisp 中表示的相同客户数据的一个示例:

[https://medium . com/media/8204 C51 C5 ba 926440210 e 1430 CDB 0 EB/href](https://medium.com/media/8204c51cc5ba926440210e1430cdb0eb/href)

下面是一个解释数据的简单 Lisp 函数:

[https://medium . com/media/771719 b 6 FD 9 c 48 ab 64 c 7141 AAC 48 a 966/href](https://medium.com/media/771719b6fd9c48ab64c7141aac48a966/href)

…以及功能和数据如何协同工作的演示:

[https://medium . com/media/8 ea a2 e 19d 14b 5 a 47 C4 ad 92 a 7016d 9 E6 b/href](https://medium.com/media/8eaa2e19d14b5a47c4ad92a7016d9e6b/href)

Lisp 列表中的第一个元素很重要。在代码中，它是可执行的“形式”(一个函数)的开始，但在数据中，它通常作为一个标签，以某种方式与列表中的后续元素相关联。如上面的代码所示，“assoc”函数通过测试每个子列表的第一个元素来查找数据。这相当于其他编程语言中的字典查找。

这种数据和代码的等价性在很大程度上延续到了 Javascript。JSON 不仅与 Javascript 对象文字的表示非常相似(但不完全相同),而且它还是可解析的 Javascript 代码。几年前，使用内置的 Javascript“eval()”函数来计算 JSON 数据并将其转换为对象文字是很常见的。

eval()函数在 Lisp 中也是标准的。它可能是第一种使用 REPL 或读取-评估-打印循环的编程语言。如今，对从外部来源提交的任意数据使用 eval()被认为是一种安全风险，但是更新的(也更安全的)JSON.parse()方法符合这个目的。还有一个 function 对象，它提供了一种将字符串转换成 Javascript 函数的方法——这再次体现了代码和数据的双重性，这种双重性始于 Lisp，延续到今天的 Javascript 中。

### 我们今天在哪里

JSON 使用更简单的语法来表示软件开发中两种最基本的数据结构:字典和数组。它与 Javascript 语法的紧密结合使其成为许多应用程序数据格式的理想选择。解析 JSON 数据非常简单，只需使用 JSON.parse()将其转换为 Javascript，然后将结果作为常规 Javascript 对象进行遍历。

它在语法上比 XML 更简单，元素对元素，占用更少的空间来捕获数据集合，使标记不那么密集，更容易让人阅读。JSON 的特性，如显式数组和将数据对象属性明确表示为 Javascript 属性，促进了更简单、更清晰的语法。

然而，XML 今天并没有消亡。使用 RSS 的网站聚合仍然被广泛使用(这是 WordPress 的一个基本特性，它驱动了今天的许多网站)，最近的一篇文章暗示它可能会卷土重来。电子数据交换(EDI)仍然被大公司广泛使用。最近一篇关于 [NotPetya 勒索软件攻击](https://www.wired.com/story/notpetya-cyberattack-ukraine-russia-code-crashed-the-world/)的报道讲述了国际航运公司马士基(Maersk)如何在他们的航运和物流 EDI 不再运行时关闭数日，导致集装箱卡车在航运码头排队，世界各地的交付停滞。

但是将对象之间的关联表示为嵌套层次结构并不适合某些应用程序领域。一个例子是社交网络数据，GraphQL(由脸书倡导，仍然使用类似 JSON 的表示)通常是一种选择。

[RDF](https://www.w3.org/RDF/) (由 [W3C 语义网组](https://www.w3.org/standards/semanticweb/)开发的基于 XML 的表示)也使用“(主语、谓语、宾语)”三元组来表示数据的非层次图，其中“宾语”部分可以是对另一个三元组的引用，以定义数据之间关系的一般图。它被用于[许多网络项目](https://en.wikipedia.org/wiki/Resource_Description_Framework)。

最初在 XML 中使用的命名空间现在也出现在 HTML 的标记数据中(例如，像 twitter 和脸书卡片标记中的“Twitter:”和“og:”命名空间这样的语义标记)。

但是，对于许多应用程序来说，JSON 极大地简化了基于互联网的软件系统的实现。这是一个 Javascript 世界，JSON 扮演着重要的角色！

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *