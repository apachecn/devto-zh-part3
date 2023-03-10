# PyMongo 教程:在 Python 应用程序中测试 MongoDB 故障转移

> 原文：<https://dev.to/scalegrid/pymongo-tutorial-testing-mongodb-failover-in-your-python-app-4h3p>

[![PyMongo Tutorial: Testing MongoDB Failover in Your Python App](img/681e6f55c0f8eef6ad3ed0edad2a0b6c.png)](https://scalegrid.io/blog/pymongo-tutorial-testing-mongodb-failover-in-your-python-app/)

Python 是一种强大而灵活的编程语言，被全球数百万开发人员用来构建他们的应用程序。毫不奇怪，Python 开发人员通常利用托管[最受欢迎的 NoSQL 数据库](https://scalegrid.io/blog/2019-database-trends-sql-vs-nosql-top-databases-single-vs-multiple-database-use/)的 [MongoDB 来进行部署，这是因为它的灵活性和缺乏模式要求。](https://scalegrid.io/mongodb.html "MongoDB Hosting")

那么，使用 MongoDB 和 Python 的最佳方式是什么呢？PyMongo 是一个 Python 发行版，包含使用 MongoDB 的工具，以及推荐的 [Python MongoDB](https://docs.mongodb.com/ecosystem/drivers/python/) 驱动程序。这是一个相当成熟的驱动程序，支持[大多数常见的数据库](https://api.mongodb.com/python/current/examples/index.html)操作，您可以查看这篇[教程](https://api.mongodb.com/python/current/tutorial.html)了解 PyMongo 驱动程序的介绍。

当在生产环境中部署时，强烈建议设置一个 [MongoDB 副本集配置](https://scalegrid.io/blog/geo-distributed-mongodb-replica-sets-for-100-uptime/)，这样您的数据就可以在地理上分布以获得高可用性。还建议启用 SSL 连接来加密客户端-数据库流量。我们经常对各种 MongoDB 驱动程序的[故障转移](https://docs.mongodb.com/manual/reference/glossary/#term-failover)特性进行测试，以证明它们适合生产用例，或者当我们的客户向我们寻求建议时。在本文中，我们将向您展示如何使用 PyMongo 连接到配置了自签名证书的支持 SSL 的 MongoDB 副本集，以及如何在您的代码中[测试 MongoDB 故障转移行为。](https://scalegrid.io/blog/pymongo-tutorial-testing-mongodb-failover-in-your-python-app/)

## 使用自签名证书连接到 MongoDB SSL

第一步是确保安装了 PyMongo 及其依赖项的正确版本。[本指南](https://api.mongodb.com/python/current/examples/tls.html#dependencies)帮助您整理依赖关系，驱动程序兼容性列表可在[这里](https://docs.mongodb.com/ecosystem/drivers/python/#python-driver-compatibility)找到。

mongo_client。我们感兴趣的 MongoClient 参数是 **ssl** 和 **ss_ca_cert** 。为了连接到使用自签名证书的支持 SSL 的 MongoDB 端点，必须将 **ssl** 设置为 **True** 并且 **ss_ca_cert** 必须指向 [CA](https://en.wikipedia.org/wiki/Certificate_authority) 证书文件。

如果您是 [ScaleGrid](https://scalegrid.io) 的客户，您可以从 [ScaleGrid 控制台](https://console.scalegrid.io)为您的 MongoDB 集群下载 CA 证书文件，如下所示:

[![Download the CA Certificate File for Your MongoDB Clusters From the ScaleGrid Console](img/1b4524b9460a44a62abd69902454fdf6.png)](https://scalegrid.io/blog/wp-content/uploads/2018/12/SSLCert.jpg) 所以，一个连接片段看起来会像:

```
>>> import pymongo
>>> MONGO_URI = 'mongodb://rwuser:@SG-example-0.servers.mongodirector.com:27017,SG-example-1.servers.mongodirector.com:27017,SG-example-2.servers.mongodirector.com:27017/admin?replicaSet=RS-example&ssl=true'
>>> client = pymongo.MongoClient(MONGO_URI, ssl = True, ssl_ca_certs = '')
>>> print("Databases - " + str(client.list_database_names()))
Databases - ['admin', 'local', 'test']
>>> client.close()
>>>

```

如果[使用自己的自签名证书](https://scalegrid.io/blog/bring-your-own-ssl-certificates/ "MongoDB – Bring Your Own SSL Certificates")，而[主机名验证](https://wiki.openssl.org/index.php/Hostname_validation)可能会失败，那么您还必须将 **ssl_match_hostname** 参数设置为 **False** 。正如驱动文档所说，不建议这样做，因为这样会使连接容易受到[中间人攻击](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)。

## 测试故障转移行为

对于 MongoDB 部署，故障转移不像传统数据库管理系统那样被视为重大事件。虽然大多数 MongoDB 驱动程序试图抽象这个事件，但是开发人员应该理解并设计他们的应用程序来处理这种行为，因为应用程序应该预料到短暂的网络错误，并在错误渗透之前重试。

您可以通过在工作负载运行时引入故障转移来测试应用程序的弹性。引发故障转移的最简单方法是运行 [rs.stepDown()](https://docs.mongodb.com/manual/reference/method/rs.stepDown/) 命令:

```
RS-example-0:PRIMARY> rs.stepDown()
2019-04-18T19:44:42.257+0530 E QUERY [thread1] Error: error doing query: failed: network error while attempting to run command 'replSetStepDown' on host 'SG-example-1.servers.mongodirector.com:27017' :
DB.prototype.runCommand@src/mongo/shell/db.js:168:1
DB.prototype.adminCommand@src/mongo/shell/db.js:185:1
rs.stepDown@src/mongo/shell/utils.js:1305:12
@(shell):1:1
2019-04-18T19:44:42.261+0530 I NETWORK [thread1] trying reconnect to SG-example-1.servers.mongodirector.com:27017 (X.X.X.X) failed
2019-04-18T19:44:43.267+0530 I NETWORK [thread1] reconnect SG-example-1.servers.mongodirector.com:27017 (X.X.X.X) ok
RS-example-0:SECONDARY>

```

我喜欢测试司机行为的方法之一是编写一个简单的“永久”编写器应用程序。这将是一段简单的代码，除非被用户中断，否则它会一直写入数据库，并打印出遇到的所有异常，以帮助我们理解驱动程序和数据库行为。我还跟踪它写入的数据，以确保在测试中没有未报告的数据丢失。下面是我们将用来测试 MongoDB 故障转移行为的测试代码的相关部分:

```
import logging
import traceback
...
import pymongo
...
logger = logging.getLogger("test")

MONGO_URI = 'mongodb://rwuser:@SG-example-0.servers.mongodirector.com:48273,SG-example-1.servers.mongodirector.com:27017,SG-example-2.servers.mongodirector.com:27017/admin?replicaSet=RS-example-0&ssl=true'

try:
    logger.info("Attempting to connect...")
    client = pymongo.MongoClient(MONGO_URI, ssl = True, ssl_ca_certs = 'path-to-cacert.pem')
    db = client['test']
    collection = db['test']
    i = 0
    while True:
        try:
            text = ''.join(random.choices(string.ascii_uppercase + string.digits, k = 3))
            doc = { "idx": i, "date" : datetime.utcnow(), "text" : text}
            i += 1
            id = collection.insert_one(doc).inserted_id
            logger.info("Record inserted - id: " + str(id))
            sleep(3)
        except pymongo.errors.ConnectionFailure as e:
            logger.error("ConnectionFailure seen: " + str(e))
            traceback.print_exc(file = sys.stdout)
            logger.info("Retrying...")

    logger.info("Done...")
except Exception as e:
    logger.error("Exception seen: " + str(e))
    traceback.print_exc(file = sys.stdout)
finally:
    client.close()

```

这样写出来的条目看起来像:

```
RS-example-0:PRIMARY> db.test.find()
{ "_id" : ObjectId("5cb6d6269ece140f18d05438"), "idx" : 0, "date" : ISODate("2019-04-17T07:30:46.533Z"), "text" : "400" }
{ "_id" : ObjectId("5cb6d6299ece140f18d05439"), "idx" : 1, "date" : ISODate("2019-04-17T07:30:49.755Z"), "text" : "X63" }
{ "_id" : ObjectId("5cb6d62c9ece140f18d0543a"), "idx" : 2, "date" : ISODate("2019-04-17T07:30:52.976Z"), "text" : "5BX" }
{ "_id" : ObjectId("5cb6d6329ece140f18d0543c"), "idx" : 4, "date" : ISODate("2019-04-17T07:30:58.001Z"), "text" : "TGQ" }
{ "_id" : ObjectId("5cb6d63f9ece140f18d0543d"), "idx" : 5, "date" : ISODate("2019-04-17T07:31:11.417Z"), "text" : "ZWA" }
{ "_id" : ObjectId("5cb6d6429ece140f18d0543e"), "idx" : 6, "date" : ISODate("2019-04-17T07:31:14.654Z"), "text" : "WSR" }
..

```

### 处理 ConnectionFailure 异常

注意，我们捕获了 [ConnectionFailure](https://api.mongodb.com/python/current/api/pymongo/errors.html#pymongo.errors.ConnectionFailure) 异常来处理由于故障转移而可能遇到的所有网络相关问题——我们打印该异常并继续尝试写入数据库。驱动程序文档[建议](https://api.mongodb.com/python/current/api/pymongo/mongo_client.html#pymongo.mongo_client.MongoClient)如下:

> 如果操作因网络错误而失败，将引发 ConnectionFailure，客户端将在后台重新连接。应用程序代码应该处理这个异常(识别操作失败)，然后继续执行。

让我们运行它，并在它执行时进行数据库故障转移。事情是这样的:

```
04/17/2019 12:49:17 PM INFO Attempting to connect...
04/17/2019 12:49:20 PM INFO Record inserted - id: 5cb6d3789ece145a2408cbc7
04/17/2019 12:49:23 PM INFO Record inserted - id: 5cb6d37b9ece145a2408cbc8
04/17/2019 12:49:27 PM INFO Record inserted - id: 5cb6d37e9ece145a2408cbc9
04/17/2019 12:49:30 PM ERROR PyMongoError seen: connection closed
Traceback (most recent call last):
    id = collection.insert_one(doc).inserted_id
  File "C:\Users\Random\AppData\Local\Programs\Python\Python36-32\lib\site-packages\pymongo\collection.py", line 693, in insert_one
    session=session),
...
  File "C:\Users\Random\AppData\Local\Programs\Python\Python36-32\lib\site-packages\pymongo\network.py", line 173, in receive_message
    _receive_data_on_socket(sock, 16))
  File "C:\Users\Random\AppData\Local\Programs\Python\Python36-32\lib\site-packages\pymongo\network.py", line 238, in _receive_data_on_socket
    raise AutoReconnect("connection closed")
pymongo.errors.AutoReconnect: connection closed
04/17/2019 12:49:30 PM INFO Retrying...
04/17/2019 12:49:42 PM INFO Record inserted - id: 5cb6d3829ece145a2408cbcb
04/17/2019 12:49:45 PM INFO Record inserted - id: 5cb6d3919ece145a2408cbcc
04/17/2019 12:49:49 PM INFO Record inserted - id: 5cb6d3949ece145a2408cbcd
04/17/2019 12:49:52 PM INFO Record inserted - id: 5cb6d3989ece145a2408cbce

```

请注意，驱动程序需要大约 12 秒来理解新的拓扑结构，连接到新的主节点，并继续写入。引发的异常是[错误。AutoReconnect](https://api.mongodb.com/python/current/api/pymongo/errors.html#pymongo.errors.AutoReconnect) 是 ConnectionFailure 的子类。

您可以再运行几次，看看还能看到哪些异常。例如，下面是我遇到的另一个异常跟踪:

```
    id = collection.insert_one(doc).inserted_id
  File "C:\Users\Random\AppData\Local\Programs\Python\Python36-32\lib\site-packages\pymongo\collection.py", line 693, in insert_one
    session=session),
...
  File "C:\Users\Randome\AppData\Local\Programs\Python\Python36-32\lib\site-packages\pymongo\network.py", line 150, in command
    parse_write_concern_error=parse_write_concern_error)
  File "C:\Users\Random\AppData\Local\Programs\Python\Python36-32\lib\site-packages\pymongo\helpers.py", line 132, in _check_command_response
    raise NotMasterError(errmsg, response)
pymongo.errors.NotMasterError: not master

```

此异常也是 ConnectionFailure 的一个子类。

### “retryWrites”参数

测试 MongoDB 故障转移行为的另一个领域是观察其他参数变化如何影响结果。一个相关的参数是' **retryWrites** ':

> retryWrites: (boolean)在 MongoDB 3.6+上出现网络错误后，在此 MongoClient 中执行的受支持的写操作是否会重试一次。默认为 False。

让我们看看这个参数如何与故障转移一起工作。对代码所做的唯一更改是:

```
client = pymongo.MongoClient(MONGO_URI, ssl = True, ssl_ca_certs = 'path-to-cacert.pem', retryWrites = True)

```

让我们现在运行它，然后进行数据库系统故障转移:

```
04/18/2019 08:49:30 PM INFO Attempting to connect...
04/18/2019 08:49:35 PM INFO Record inserted - id: 5cb895869ece146554010c77
04/18/2019 08:49:38 PM INFO Record inserted - id: 5cb8958a9ece146554010c78
04/18/2019 08:49:41 PM INFO Record inserted - id: 5cb8958d9ece146554010c79
04/18/2019 08:49:44 PM INFO Record inserted - id: 5cb895909ece146554010c7a
04/18/2019 08:49:48 PM INFO Record inserted - id: 5cb895939ece146554010c7b <<< Failover around this time
04/18/2019 08:50:04 PM INFO Record inserted - id: 5cb895979ece146554010c7c
04/18/2019 08:50:07 PM INFO Record inserted - id: 5cb895a79ece146554010c7d
04/18/2019 08:50:10 PM INFO Record inserted - id: 5cb895aa9ece146554010c7e
04/18/2019 08:50:14 PM INFO Record inserted - id: 5cb895ad9ece146554010c7f
...

```

请注意故障转移后的插入如何花费大约 12 秒，但成功完成，因为 **retryWrites** 参数确保重试失败的写入。请记住，设置该参数并不能免除您处理 ConnectionFailure 异常的责任——您需要担心其行为不受该参数影响的读取和其他操作。它也没有完全解决问题，即使是对于受支持的操作——有时故障转移可能需要更长的时间才能完成，光靠 **retryWrites** 是不够的。

### 配置网络超时值

**rs.stepDown()** 导致相当快的故障转移，因为副本集主节点被指示成为辅助节点，并且辅助节点举行选举以确定新的主节点。在生产部署中，网络负载、分区和其他此类问题会延迟主服务器不可用的检测，从而延长故障转移时间。您还会经常遇到 PyMongo 错误，比如[错误。ServerSelectionTimeoutError](https://api.mongodb.com/python/current/api/pymongo/errors.html#pymongo.errors.ServerSelectionTimeoutError) ，错误。网络超时等。在网络问题和故障转移期间。

如果这种情况经常发生，您必须调整超时参数。相关的 MongoClient 超时参数是 serverSelectionTimeoutMS、connectTimeoutMS 和 socketTimeoutMS。其中，为 serverSelectionTimeoutMS 选择较大的值通常有助于在故障转移期间处理错误:

> serverSelectionTimeoutMS:(integer)控制驱动程序等待多长时间(以毫秒为单位)来查找可用的适当服务器以执行数据库操作。在等待期间，可能会执行多个服务器监控操作，每个操作都由 connectTimeoutMS 控制。默认为 30000 (30 秒)。

准备好在您的 Python 应用程序中使用 MongoDB 了吗？查看我们的[Python 和 MongoDB](https://scalegrid.io/blog/getting-started-with-python-and-mongodb-2/) 入门文章，了解如何通过 5 个简单的步骤开始运行。ScaleGrid 是唯一一个 [MongoDB DBaaS 提供者](https://scalegrid.io/mongodb/hosting-comparison.html)，它为您提供了对实例的完全 SSH 访问，因此您可以在与 MongoDB 服务器相同的机器上运行 Python 服务器。通过专用服务器、高可用性和灾难恢复，在 [AWS](https://scalegrid.io/mongodb/aws.html "MongoDB hosting on AWS") 、 [Azure](https://scalegrid.io/mongodb/azure.html "MongoDB hosting on Azure") 或 [DigitalOcean](https://scalegrid.io/mongodb/digitalocean.html "MongoDB hosting on DigitalOcean") 上自动化您的 MongoDB 云部署，以便您可以专注于开发您的 Python 应用程序。