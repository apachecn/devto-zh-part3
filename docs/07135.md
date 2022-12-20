# 用卡夫卡流洗去污垢第 1 部分

> 原文：<https://dev.to/jbull328/wash-away-the-crud-with-kafka-streaming-3lik>

## Kafka 与你的网络应用流。

[![Pipelines](img/df0879ede5691549e44b170b198a394e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DVr2l8QR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sierraclub.org/sites/www.sierraclub.org/files/styles/flexslider_full/public/sierra/articles/big/SIERRA%2520Pipeline%2520Sunset%2520WB.jpg%3Fitok%3D8WmuhW_d)

卡夫卡是什么？

流媒体平台有三个关键功能:

发布和订阅记录流，类似于消息队列或企业消息传递系统。以容错的持久方式存储记录流。
在记录流出现时对其进行处理。
卡夫卡一般用于两大类应用:

1.  构建实时流数据管道，在系统或应用程序之间可靠地获取数据
2.  构建实时流应用程序，对数据流进行转换或做出反应
3.  为了理解卡夫卡是如何做这些事情的，让我们深入下去，自下而上地探索卡夫卡的能力。

这段描述来自卡夫卡网站。
[https://kafka.apache.org/intro](https://kafka.apache.org/intro)

好吗？？那么，我们为什么要使用它呢？

我能想到几个用例。

1.  它在利用不易修改或移除的遗留系统方面非常有效。
2.  将大型数据集群迁移到云中。
3.  创建基于事件的高度反应系统。

当我第一次开始研究卡夫卡和它基于事件的架构时，有点困惑。我真的看不出我将如何编写程序来使用这项技术，它的好处充其量只是虚无缥缈的。但是一旦我写了一个简单的应用程序来演示如何使用事件，事情就变得清晰多了。让我们看一个简单的例子。

注意:我来自一个 web 开发背景，虽然我正在努力提高我的其他语言技能，但我最喜欢 Node.js，所以这就是我在这种情况下要达到的目的。您会注意到 Kafka 生态系统是用 Java 构建的，并且有许多针对 Java 和 Python 的工具，由于工具选项的原因，我可能会在生产中使用这两种语言中的任何一种。

第一步:建立卡夫卡式的融合环境。

这是一个有点高的酒吧，让这一切玩得很好，但由汇合提供的文档是非常好的。

链接 Ubuntu/Debian 说明因为 Linux:[https://docs . confluent . io/current/installation/installing _ CP/deb-Ubuntu . html # systemd-Ubuntu-Debian-install](https://docs.confluent.io/current/installation/installing_cp/deb-ubuntu.html#systemd-ubuntu-debian-install)

`wget -qO - https://packages.confluent.io/deb/5.2/archive.key | sudo apt-key add -`

`sudo add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/5.2 stable main"`

`sudo apt-get update && sudo apt-get install confluent-community-2.12`

安装完成后，我们可以运行

`confluent start`

，你应该会看到所有的组件都启动了。

为了让这个例子与 Kafka 一起工作，我们需要建立一个称为生产者的数据流，以及一个称为消费者的读取数据的东西。我们也希望我们的应用程序中有一些交互元素

首先，我们需要导航到 Kafka 安装位置，并运行一个命令来创建我们将用于消息的主题。这里的技巧可能是找到运行脚本的安装位置，但是一旦找到，我们将如下设置一个简单的主题。

`./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic employees`

然后，我们将创建一个 Kafka 生产者，让我们的一些数据流入事件系统。

```
//employeeProducer.js
const fs = require("fs");
const parse = require("csv-parse");

const kafka = require("kafka-node");
const Producer = kafka.Producer;
const client = new kafka.Client("localhost:2181");

const topic = "employees";

(KeyedMessage = kafka.KeyedMessage),
  (producer = new Producer(client)),
  (km = new KeyedMessage("key", "message")),
  (testProducerReady = false); 
```

Enter fullscreen mode Exit fullscreen mode

这是设置我们将处理哪个 Kafka 主题以及在哪里可以找到 Kafka 集群的配置材料。把主题想象成一个数据库，一条消息就相当于一条记录。

接下来，我们将使用我们导入的 Kafka Producer 包提供的一些方法。我们还将解析一个包含一些员工数据的 CSV 文件。我使用这个网站 [Mockaroo](https://www.mockaroo.com/) 为我创建了一些数据，只有名字、姓氏、姓名和雇佣日期，并将其保存在我的应用程序文件夹/datafiles/的目录中。

```
producer.on("ready", function() {
  console.log("Producer for tests is ready");
  testProducerReady = true;
});

producer.on("error", function(err) {
  console.error("Problem with producing Kafka message " + err);
});

//we are bringing in some data in a csv, as in this example of a legacy environment that is how it is done.

const inputFile = "./dataFiles/MOCK_Employee_DATA.csv";

let dataArray = [];

let parser = parse({ delimiter: "," }, function(err, data) {
  dataArray = data;
  handleData(1);
});

fs.createReadStream(inputFile).pipe(parser); 
```

Enter fullscreen mode Exit fullscreen mode

函数 handleData 将为我们创建一个键值对的数据节点，我们在 produceMessage 函数中使用它来传递给我们的 Kafka 生成器。我在数据上设置了一个超时时间，只是为了在控制台中查看数据流，这样我们就可以看到发生了什么。

```
const handleData = currentData => {
  let line = dataArray[currentData];
  let dataNode = {
    f_name: line[0],
    l_name: line[1],
    hire_date: line[2],
    event_id: "emp_chg_01"
  };
  console.log(JSON.stringify(dataNode));
  produceDataMessage(dataNode);
  let delay = 20;
  setTimeout(handleData.bind(null, currentData + 1), delay);
}

//create Keyed message from parsed json data and send it to kafka
const produceDataMessage = dataNode => {
  (KeyedMessage = kafka.KeyedMessage),
    (dataNodeKM = new KeyedMessage(dataNode.code, JSON.stringify(dataNode))),
    (payloads = [{ topic: topic, messages: dataNodeKM, partition: 0 }]);
  if (testProducerReady) {
    producer.send(payloads, function(err, data) {
      console.log(data);
      if (err) {
        console.log(
          "Sorry, TestProducer is not ready yet, failed to produce message to kafka with error ==== " +
            err
        );
      }
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在运行它，我们应该会看到一些消息，其中包含我们文件中的数据，如下所示。

```
{ employees: { '0': 948 } }
{"f_name":"Fax","l_name":"Giraudat","hire_date":"7/20/2014","event_id":"emp_chg_01"}
{ employees: { '0': 949 } }
{"f_name":"Brande","l_name":"Jewks","hire_date":"6/4/2016","event_id":"emp_chg_01"}
{ employees: { '0': 950 } }
{"f_name":"Bel","l_name":"Bromfield","hire_date":"8/10/2016","event_id":"emp_chg_01"}
{ employees: { '0': 951 } }
{"f_name":"Christophorus","l_name":"Kimbrey","hire_date":"6/4/2015","event_id":"emp_chg_01"}
{ employees: { '0': 952 } }
{"f_name":"Inga","l_name":"Reedyhough","hire_date":"7/12/2012","event_id":"emp_chg_01"} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有这些，我们的情况会很好。我们已经设置了 Kafka，我们可以将数据从我们的脚本流式传输到群集，接下来我们将创建一个消费者来将这些消息公开给前端，最后我们将创建一个基于事件的系统，该系统可以根据事件 id 对事件做出反应。

[第二部](https://dev.to/jbull328/wash-away-the-crud-with-kafka-streaming-part2-5ekb)
第三部