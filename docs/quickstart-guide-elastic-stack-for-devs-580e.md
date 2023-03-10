# 快速入门指南:面向开发人员的弹性堆栈

> 原文：<https://dev.to/_codingblocks/quickstart-guide-elastic-stack-for-devs-580e>

## 对弹力栈好奇？这个快速指南将带你完成设置，并从我最喜欢的开发者那里导入文章，这样你就可以到处玩了！

Elasticsearch 是一个开源、可扩展的全文搜索和分析引擎。它有多种用途，从[全文搜索，到电子商务，到实时分析](https://analytics.codingblocks.net/blog/elasticsearch-is-not-just-for-search/)。它经常与大组织和大数据联系在一起，但 Elastic 在默认配置方面做得很好，因此对于较小的项目也很容易。

[https://www.youtube.com/embed/Zaqe5rFtma4](https://www.youtube.com/embed/Zaqe5rFtma4)

在本指南中，我们将构建一个基本的弹性堆栈(以前称为“ELK 堆栈”)，它将一些不同的开源服务整合在一起，这些服务旨在协同工作。以下是这些服务的概要描述:

**Elasticsearch** 是一个持久性引擎和 API 层。
**Logstash** 是一个基于插件的数据导入工具
**Kibana** 是一个用于浏览和管理的管理 GUI

如果你喜欢看而不喜欢读，我还录了一段视频:

[![Screenshot of Elastic Stack with developer blog data](img/f2ce6068c97ff3ce41613ca8ec452437.png "The results: Elastic Stack running with dev blog data")](https://res.cloudinary.com/practicaldev/image/fetch/s--ym8D238---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://analytics.codingblocks.net/img/elasticsearch-quickstart-complete.png)

## 先决条件

您需要本指南的 Docker，查看此处的[安装说明](https://docs.docker.com/install/)。

如果你使用的是 Windows，那么我推荐使用 Linux 容器(默认),并且[共享一个驱动器](https://blogs.msdn.microsoft.com/wael-kdouh/2017/06/26/enabling-drive-sharing-with-docker-for-windows/),这样 Docker 就可以把你的弹性数据保存到磁盘上。

## #1:构建架构

第一步是创建一个 docker-compose.yml 文件，描述您的服务如何组合在一起。在这个指南中，我的目标是速度超过深度，所以我已经为你创建了这个。

简单地克隆存储库并启动弹性堆栈:

`git clone https://github.com/codingblocks/simplified-elastic-stack.git
cd simplified-elastic-stack
docker-compose up -d`

在 Elasticsearch 启动后，给它几秒钟的时间让它喘口气，然后你可以通过点击这个 url 来验证它的状态

[![Screenshot of successful status message](img/6f599481cb91bbea6a5ed68b4b21f718.png "Congratulations, you have the Elastic stack running on you computer!")](https://res.cloudinary.com/practicaldev/image/fetch/s--ZKq-CMkV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://analytics.codingblocks.net/img/200-status.png)

注意:logstash 容器将很快关闭，因为它还没有做任何事情，所以让我们来解决这个问题！

## #2:导入数据

现在我们将使用 Logstash 导入数据。您在上面克隆的 repo 已经有一个自定义 Dockerfile 文件。Logstash 文件，所以让我们添加一个可以导入 [RSS](https://www.copyblogger.com/what-the-heck-is-rss/) 提要的输入插件。您所要做的就是将第二行添加到 Dockerfile 中。Logstash 使它看起来像这样:

`FROM docker.elastic.co/logstash/logstash-oss:7.0.0
RUN bin/logstash-plugin install logstash-input-rss`

现在，让我们添加几个输入配置。每个“rss”块代表每 3600 秒导入到 Elasticsearch 中的一个 RSS 提要。用下面几行替换 config/logstash.conf 文件的内容，logstash 会完成剩下的工作。

你可以看到输入是一些我最喜欢的博客，配置为每小时一次投票。输出建立了一个名为“博客”的基本索引来保存数据。

**更新 config/logstash.conf:**

```
input {
  rss {
    url => "https://dev.to/feed/davefollett"
    interval => 3600
  }

  rss {
    url => "https://dev.to/feed/dance2die"
    interval => 3600
  }

  rss {
    url => "https://dev.to/feed/dotnetcoreblog"
    interval => 3600
  }
  rss {
    url => "https://dev.to/feed/kritner"
    interval => 3600
  }

  rss {
    url => "https://dev.to/feed/molly_struve"
    interval => 3600
  }

  rss {
    url => "https://dev.to/feed/rionmonster"
    interval => 3600
  }

  rss {
    url => "https://dev.to/feed/TheInfraDev"
    interval => 3600
  }

  rss {
    url => "https://dev.to/feed/thejoezack"
    interval => 3600
  }
}

output {
  elasticsearch {
    action => "index"
    index => "blogs"
    hosts => "elasticsearch:9200"
    document_id => "%{[link]}"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，Logstash 会处理好其他的事情。下次我们重新启动环境时，Logstash 将开始轮询并导入提要数据。
停止、重建和重启您的环境:

`docker-compose down
docker-compose build
docker-compose up -d`

在 docker-compose 运行后给 Elasticsearch 一分钟喘息的时间，并尝试在浏览器中点击这个 url，看看是否有数据:[http://localhost:9200/blogs/_ search](http://localhost:9200/blogs/_search)

## #3:玩得开心！

现在一切都准备好了，所以是时候进行一些探索了。
如果你是弹性堆栈的新手，我建议你先熟悉一下 Kibana。它已经在你的计算机上运行了:[http://localhost:5601](http://localhost:5601)
去“开发工具”那里试一试这些查询，这样你就能体会到 Elastic 提供了什么。

#### 样本查询

简单过滤，获取去年关于 JavaScript 的前 5 篇文章

```
GET /blogs/_search?q=JavaScript&size=5
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "published": {
              "gte" : "now-1y/y",
            }
          }
        }
      ]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

简单汇总，按日期发布

```
GET /blogs/_search?size=0
{
  "aggs":{
    "posts by date":{
      "date_histogram":{
        "field":"published",
        "interval":"year"
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

组合聚合/过滤器:Elasticsearch 的前 10 个结果，按作者列出结果和计数

```
GET /blogs/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "message": "Elasticsearch"
          }
        }
      ]
    }
  },
  "aggs": {
    "author": {
      "terms": {
        "field": "author.keyword"
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经建立并运行了 Elastic Stack，下面是一些建议:

*   建立一个简单的网站，让你浏览和搜索你最喜欢的博客。
*   探索[搜索](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-search.html)和[聚合](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)API *[使用 Kibana](https://www.elastic.co/guide/en/kibana/current/visualize.html) 创建可视化

[![Screenshot of Elastic Stack with developer blog data](img/f2ce6068c97ff3ce41613ca8ec452437.png "The results: Elastic Stack running with dev blog data")](https://res.cloudinary.com/practicaldev/image/fetch/s--ym8D238---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://analytics.codingblocks.net/img/elasticsearch-quickstart-complete.png)

## 故障排除:(非常感谢[戴夫·福莱特](https://dev.to/davefollett)的帮助！)

*   运行在 linux 上，而 Elasticsearch 容器一直崩溃？您的 vm.max_map_count 可能太低。请查看相关解释/修复:[https://www . elastic . co/guide/en/elastic search/reference/current/VM-max-map-count . html](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html)
*   集装箱有问题吗？“码头工人日志”和“码头工人检查”是你的朋友。您可以运行这样的命令来查看所有的 elasticsearch 文件在磁盘上的位置:“docker inspect $(docker PS-f name = elastic search-AQ)”

照片由 Unsplash 上的 Nine Kö pfer 拍摄