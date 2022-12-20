# 使用 curl 记录到弹性搜索

> 原文：<https://dev.to/hoverbaum/log-to-elasticsearch-using-curl-4dpi>

一旦你有了像 Elasticsearch setup 这样的集中日志解决方案，你就有了无限的可能性。也就是说，如果你真的发送日志到你的中心位置。在最近的一个项目中，我们想

> “只需从 Jenkins 服务器记录我们的部署”。

因此，我们开始寻找“最简单”的方法来登录我们的弹性搜索。我们最终找到了 curl 作为我们的解决方案，今天我将带您了解如何做到这一点。

这个帖子用的是 Elasticsearch 第 7 版。我们还关注 Mac 和 Linux 终端的命令。其中一些在 Windows 上的工作方式可能不同，我建议使用 [Git Bash](https://gitforwindows.org/) 来解决这些问题。

## 控制台基础知识

如果像我一样，您发现命令行非常吸引人，因为它有无数的工具，以及如何将简单的工具组合成更大的工作流，就像好的软件一样，但您不知道自己实际在做什么(也像我一样)，请跟随我们深入了解一些我们今天将使用的工具。如果你是一个坚定的终端英雄，请随意跳过这一部分。

### 卷曲

curl 是一个免费的开源命令行工具，用于在互联网上传输数据。因此，您可以使用它向服务器发出 GET 请求或 POST 请求。让我们看一个你可以尝试的简单例子:

```
curl https://jsonplaceholder.typicode.com/posts/1 
```

这将向 [JSON 占位符 API](https://jsonplaceholder.typicode.com/) 发出一个简单的 get 请求，并返回索引为 1 的帖子。curl 会将结果记录到控制台中。通过这个日志，到控制台，你也可以用其他命令链接 curl，比如 grep 文章的标题。但是更深入的链接是另一天的主题。

```
curl https://jsonplaceholder.typicode.com/posts/1 | grep title 
```

相反，让我们专注于更好地理解旋度和相关选项。首先，我们需要指定我们想要使用的方法(POST)。为此，我们可以使用`-X`(注意大写的 X)选项，并传入我们想要使用的方法。我们之前的占位符 API 也允许我们发布到它，所以让我们试试:

```
curl -X POST https://jsonplaceholder.typicode.com/posts 
```

现在我们想发送一些数据。为此，curl 有`-d`来传递数据，还有`-H`来设置报头。我们想为[内容类型](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)设置一个头，这样我们就可以告诉接收者我们正在发送 JSON。尝试下面的代码，并且在没有`-H`选项的情况下也尝试一下，感受一下我们通过使用它得到了什么。您会注意到，占位符 API 将整个数据解释为一个没有指定值的键。设置正确的内容类型有助于我们以服务器可以处理的方式传递数据。你可能会遇到一个关于 Windows 的[问题。](https://stackoverflow.com/a/22883631/2156675) 

```
curl -X POST https://jsonplaceholder.typicode.com/posts -H "Content-Type: application/json" -d '{"title": "My awesome title"}' 
```

阅读卷曲手册 [-X](https://curl.haxx.se/docs/manpage.html#-X) 、 [-H](https://curl.haxx.se/docs/manpage.html#-H) 和 [-d](https://curl.haxx.se/docs/manpage.html#-d) 中所用选项的更多信息

### 时间戳和环境变量

我们想添加到所有日志中的一件事是时间戳。幸运的是，我们的终端带有`date`，可以以多种格式打印当前日期。你可以参考 [Ubuntu docs on date](https://manpages.ubuntu.com/manpages/bionic/en/man1/date.1.html) 获取关于日期的文档，以及这篇[关于格式化日期的文章](https://www.cyberciti.biz/faq/linux-unix-formatting-dates-for-display/)来获得一些灵感。一般来说，你调用`date`时只需要传递一个参数。`date +%s`例如给你自纪元以来的秒数。

然而对于 Elasticsearch，我们需要从 Epoch 开始以毫秒为单位的[日期字段](https://www.elastic.co/guide/en/elasticsearch/reference/current/date.html)。我们的时间戳将是一个日期类型的字段，这样我们就可以根据它进行过滤。为了达到这个目的，我们将加上纳秒，然后除以 1000000。因为`date`在不同的系统上表现稍有不同，下面的命令将简单地在不支持日期命令的毫秒系统上用零填充秒和毫秒之间的特异性(看看你 MAC OS-[stack overflow credit](https://apple.stackexchange.com/a/135743))。

```
echo $(($(date +'%s * 1000 + %-N / 1000000'))) 
```

快速浏览一下`$()`:这个语法允许我们使用前面命令的返回值作为下一个命令的输入。查看下面的命令行输入示例，感受一下这意味着什么。

```
export MY_ENV=date
echo $MY_ENV
# date
export MY_ENV2=$(date)
echo $MY_ENV2
# Thu  9 May 2019 12:01:14 CEST 
```

在我们的具体例子中，嵌套块返回以下内容:

```
echo $(date +'%s * 1000 + %-N / 1000000')
# 1557396103 * 1000 + N / 1000000
echo $($(date +'%s * 1000 + %-N / 1000000'))
# command not found: 1557396188
echo $(($(date +'%s * 1000 + %-N / 1000000')))
# 1557396266000 
```

现在我们有了时间戳，我们希望在向服务器的 POST 请求中使用它。为此，我们将把它保存在一个环境变量中，并在我们的请求中引用它。

```
NOW=$(($(date +'%s * 1000 + %-N / 1000000'))) && curl -X POST https://jsonplaceholder.typicode.com/posts -H "Content-Type: application/json" -d '{"timestamp": "'"${NOW}"'"}' 
```

让我来解释一下:首先，我们将时间戳分配给一个名为*的环境变量，现在是*，然后我们告诉我们的 shell 在不丢失上下文的情况下执行另一个命令，在这里我们通过`&&`发出 POST 请求。`&&`方法并非在所有平台上都是必要的，但是我们在这里使用它来保持命令与 Linux 和 Mac 兼容。你通常会期望在你想要插入环境变量值的地方看到`${NOW}`。但是为了让我们的 shell 在这里正确转义，我们需要将我们的值包装到包装好的单引号中。

操作系统兼容性和转义 JSON 值。Windows 在命令中不支持单引号`'`，所以你必须使用类似于`"{\"title\": \"My awesome title\"}"`的东西。您也可以在 MAC 和 Linux 机器上使用该语法，但是我发现单引号方法更好读，尽管它对环境变量进行了转义。由你决定。

## 弹性搜索 API

Elasticsearch 为如何开始理解、创建和搜索数据提供了很好的文档。

[![Let's take a look at those internals](img/52c7903ebad590e1b2ec7ff5e884bbd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gtAjnP31--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hoverbaum-blog-assets/general-images/internals-magnified.jpg)

### 索引和文档

首先，让我们回顾一下 Elasticsearch 是如何组织数据的。Elasticsearch 中的基本元素称为*文档*。你可以把它想象成一个通过 Elasticsearch 记录、处理和组织的单一事件。它们仍然按照索引中分组的类型来组织。[类型，然而，在 Elasticsearch 6 中已经被否决](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started-concepts.html#_type)(在编写第 7 版时是当前版本),这导致当前每个索引只允许一种类型。另一方面，索引是相似文档的集合，您可以拥有任意多的索引。您将需要它们来插入和查询文档。

### 局部弹性搜索

如果您想了解这里的例子，可以使用 Docker 在本地运行 Elasticsearch，或者用运行 Elasticsearch 的域替换 localhost 域。使用 Elasticsearch 对 Docker 容器运行以下命令，如果遇到麻烦，请查看 [Elasticsearch Docker 文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)。

```
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.0.0 
```

以下所有示例现在都将 localhost 称为 Elasticsearches 域，因此您可以复制并粘贴它们进行尝试。如果您想在您的环境中使用代码片段，请记住更新这个。

### 插入文件

你可以 post 到任何一个`/index/type`来添加一个新的文档到特定类型的索引中。如果你想得到一个漂亮的响应，在 URL 中添加`?pretty`。

比方说，您想要一个索引，将与应用程序部署相关的文档进行分组。您最初想要记录的对象可能包含您正在部署的代码的散列，因此看起来像:

```
{  "gitHash":  "1234567"  } 
```

现在要将这个文档添加到使用 *_doc* 作为标准类型的*部署*索引中(记住[已被否决，但仍然需要](https://www.elastic.co/guide/en/elasticsearch/reference/7.0/getting-started-concepts.html#_type)),您可以简单地用您的数据调用`/deploys/_doc`端点。

```
curl -X POST "localhost:9200/deploys/_doc/?pretty" -H 'Content-Type: application/json' -d'
{
  "gitHash": "1234567"
}
' 
```

如果你不是在本地运行 Elasticsearch，而是在一个远程服务器上，用你的域名替换上面的`localhost:9200`。

### 阅读文档

现在我们也可以通过查询`/index/_search`端点来获得我们刚刚添加的文档。

```
curl -X GET "localhost:9200/deploys/_search/?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} }
}
' 
```

这将返回索引中的所有文档。查看[搜索 API](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started-search-API.html) 获得更多选项。

### 映射

当我们注意到 Elasticsearch 期望时间戳(或一般的日期)从 Epoch 开始以毫秒为单位时，我们之前简单地提到了映射。通过映射，我们可以向索引添加字段。有了这个，我们就可以控制一个字段将得到的*类型*。虽然所有字段都默认为“text ”,但是在将第一个文档添加到索引之前，将字段设置为“date”类型对于我们的时间戳来说尤其重要。通过这样做，我们确保以后可以正确地过滤我们在 Kibana 中的时间戳。

遗憾的是，这意味着我们现在需要再次删除我们的索引(以及我们可能在 Elasticsearch 索引之前创建的任何 Kibana 索引)。

```
curl -X DELETE "localhost:9200/deploys" 
```

现在，我们可以使用:
为时间戳添加正确映射的索引

```
curl -X PUT "localhost:9200/deploys" -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "properties": {
      "timestamp": {
        "type": "date"
      }
    }
  }
}
' 
```

您可以参考关于日期数据类型的[文档了解更多信息。](https://www.elastic.co/guide/en/elasticsearch/reference/7.0/date.html)

## 使用 curl 进行日志记录

既然我们已经有了 curl 和 date down 的基础知识，以及对 Elasticsearches API 的理解，现在终于到了进行日志记录的时候了。

我们已经锁定了通过 POST 请求发送包含时间戳的消息。有了 Elasticsearch API 的知识，我们现在可以用时间戳记录事件。

```
NOW=$(($(date +'%s * 1000 + %-N / 1000000'))) && \
curl -H "Content-Type: application/json" \
-X POST "http://localhost:9200/deploys/_doc" \
-d'
  { 
    "timestamp": "'"${NOW}"'"
  }
' 
```

有了以上内容，你应该对这里发生的事情有了扎实的了解。下面我们将深入一个更复杂的例子，并再次解释发生了什么。

### 实际例子

上面我用了一个例子来记录包含与部署相关的 git 散列的内容。事实上，这是我深入研究这个问题的用例。让我向您展示我的解决方案，将当前部署的 git hash 从我们的 CI 服务器记录到 Elasticsearch。以下命令需要从 git 存储库中运行。

```
HASH=$(git rev-parse --short HEAD) && \
NOW=$(($(date +'%s * 1000 + %-N / 1000000'))) && \
curl -H "Content-Type: application/json" \
-X POST "http://localhost:9200/deploys/_doc" \
-d '
  { 
    "gitHash" : "'"${HASH}"'",
    "timestamp": "'"${NOW}"'",
    "info": "some infos",
    "environment": "test"
  }
' 
```

你大概能搞清楚这是怎么回事。让我过一遍，以确保我们在同一页上。首先，我们总共有三个命令，让我们一次检查一个。

`HASH=$(git rev-parse --short HEAD)`将我们当前所在的 git 库的短散列(前 7 个符号)分配到一个名为 *HASH* 的环境变量中。

`NOW=$(($(date +'%s * 1000 + %-N / 1000000')))`看起来应该很熟悉，它以毫秒为单位获取当前时间戳，并将它赋给一个名为*的变量，现在名为*。

最后，我们在一个大的 curl 命令中把所有这些放在一起。让我看一下这个的选择。

`-H "Content-Type: application/json"`用“application/json”设置一个“Content-Type”头，这样我们的 Elasticsearch 就知道它得到了有效的 json。

`-X POST "http://your.domain:9200/deploys/deploy"`告诉 curl 触发 POST 请求以及使用哪个端点。

`-d '{ "gitHash" : "'"${HASH}"'", "timestamp": "'"${NOW}"'", "info": "some infos", "environment": "test"}'`最终定义了我们将随请求一起发送的 JSON 数据。这里我们使用我们创建的变量来填充 git 散列和时间戳。请注意，我们再次使用`"'"`进行适当的转义。

## 结论

今天我们学习了很多关于旋度、日期和弹性搜索的知识。我们不仅研究了如何单独使用这些工具，还研究了如何一起使用它们来“快速记录一些东西”。

我希望这能帮助你做大事，并通过你现有的系统获得立竿见影的效果。

[![](img/1da0db3f26eeffca461e7d06409d80ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gzXx36Ft--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hoverbaum-blog-assets/emojies/emoji-memo.png)