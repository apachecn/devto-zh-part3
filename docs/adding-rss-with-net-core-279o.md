# 使用添加 RSS。网络核心

> 原文：<https://dev.to/zeerorg/adding-rss-with-net-core-279o>

这是我在这个[博客](https://blog.zeerorg.site)上的主要项目的第三篇文章。

RSS 是一项古老的技术，多年来已经逐渐淡出了一般的生态系统。随着谷歌舒婷关闭其谷歌阅读器服务，火狐[在 2018 年的最后一次发布中停止了对 RSS 的支持](https://dev.to/sudiukil/firefox-64-drops-rss-support-thoughts-4hbo)。我承认我从来没有真正进入过 RSS，为不同的博客建立一个阅读清单。我不是一个经常阅读博客的人，我的大部分新闻来源是通过应用程序，这意味着我从来没有真正需要 RSS。

所以当我想到在我的网站上添加 RSS 时，这实际上是我深思熟虑后做出的决定。实际上，我尝试了一下 [Feedly](https://feedly.com/) 来了解现代 RSS 阅读器是如何工作的。我当然明白为什么很多人喜欢它，这也是我想继续做下去的第二个原因。最后一个原因纯粹是因为我想学习更多关于 RSS 和 XML 的知识。这将是我第一次实现基于 XML 的数据模式。

## [T1。净核心辛迪加饲料](#net-core-syndicationfeed)

中为 RSS 提要实现助手类的主库。NET 是[系统。联合企业](https://docs.microsoft.com/en-us/dotnet/api/system.servicemodel.syndication?view=netcore-2.2)。顶层的类是`SyndicationFeed`,它不仅可以序列化为最新的 RSS 2.0 标准，还可以序列化为 Atom 1.0 feed，因此您可以选择自己想要的。您需要通过 [NuGet](https://www.nuget.org/packages/System.ServiceModel.Syndication/) 添加图库模块。

首先，我们创建一个[联合提要](https://docs.microsoft.com/en-us/dotnet/api/system.servicemodel.syndication.syndicationfeed?view=netcore-2.2)对象，并向其添加初步信息。比如博客的标题、描述和网址。然后我们添加作者，在这里我们可以指定多个作者。

```
// Top level feed setup
SyndicationFeed feed = new SyndicationFeed(title, description, url);
feed.Language = "en-us";
feed.LastUpdatedTime = DateTime.Now;

// Adding Authors
SyndicationPerson sp = new SyndicationPerson("r.g.gupta@outlok.com", "Rishabh Gupta", "https://zeerorg.site/");
feed.Authors.Add(sp); 
```

RSS 中的每个`<item>`元素都被表示为 [SyndicationItem](https://docs.microsoft.com/en-us/dotnet/api/system.servicemodel.syndication.syndicationitem?view=netcore-2.2) 类的一个对象。可以将项目添加到提要对象中。

```
SyndicationItem item = new SyndicationItem(title, description, url);
item.LastUpdatedTime = DateTimeOffset.FromUnixTimeSeconds(timestamp);

feed.items.Add(item); 
```

在`<item>`元素中有一个重要但可选的元素是`<content:encoded>`元素，它只对那些需要提供文章内容和更新的提要重要。但是在 api 内部并不直接支持这样的设置，所以我们需要创建一个自定义元素并将其分配给项目。

```
// The third argument specifies
XMLDocument doc = new XmlDocument()
XMLElement content = doc.CreateElement("content", "encoded", "http://purl.org/rss/1.0/modules/content/");
content.InnerText = htmlContent;
item.ElementExtensions.Add(content); 
```

设置好提要后，可以将其序列化为 RSS 2.0 标准。

```
StringWriter sw = new StringWriter();
XmlWriter rssWriter = XmlWriter.Create(sw);

// The second false specifies the format for feed.
// As it can be converted to an older RSS 2.0 format or the newer a10 format.
Rss20FeedFormatter rssFormatter = new Rss20FeedFormatter(feed, false);
rssFormatter.WriteTo(rssWriter);
rssWriter.Close();
string FeedString = sw.ToString(); 
```

## 为我的博客获取 RSS 源

对于我的博客，所有的元数据都存储在一个从 blob 存储器中取出的`main.json`文件中。这是一个非常初级的内容管理系统。json 包含一个作为 json 对象的帖子列表，每个条目包含与该帖子对应的数据。存储的数据之一是`filename`,它指定了包含要从 blob 容器中获取的 HTML 的文件名。RSS 提要生成器为`<item>`元素从每个帖子的 JSON 对象中提取数据，并使用`filename`属性设置从 blob 容器中获取的 html 内容。

首先，我曾经在每次请求时触发这个函数，但是经过一些测试后，我意识到动态创建 rss 提要是一个代价很高的操作，所以我在每次更改`main.json`时触发创建 RSS，并将结果保存在一个 blob 中，直接提供服务。

点击这里查看我的 rss 订阅源。