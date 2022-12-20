# MongoDB REST API

> 原文：<https://dev.to/delbussoweb/mongodb-rest-api-24np>

大家好，我很高兴我的最后一篇文章的影响我开始了一个系列关于 mongodb connector for bi。反馈一直很好，很快第二个帖子就来了。但是，有些问题表明，BI Connector 不是连接 mongodb 实例和 BI 工具的唯一方法，但为什么？因为还有其他的 API！那我们走吧-我...。

### 说得很清楚

在讨论其中一种替代方案之前，我们需要了解 BI Connector 和其他 API 之间的一些要点和区别。

最重要的是，BI Connector 以及用于 mongodb 的 OBDC 驱动程序是按照标准和最佳数据访问方式开发的。有多种方法可以访问数据并开发驱动程序，因此 BI Connector 和 ODBC 驱动程序使用这些标准比其他可用 API 更有效地传递数据。但为什么它们存在呢？

提供了一种在应用程序之间快速访问和交换数据的方法，从而简化了开发人员的生活，这也是他们的主要目标:在应用程序之间交换数据！通过这些 API，您可以使用 HTTP 方法在 mongodb 中进行请求，其中大多数提供了从简单的 CRUD 到群集管理命令的各种功能！

但是 Leandro，因为主要目标是应用程序之间的数据交换，所以我的 BI 工具是否不适合？是啊！但这不是最好的办法！REST API 提供了 JSON 返回功能，可由工具 100%处理，而驱动程序则提供了与更高级的愿望选项更好的交互。因此，如果要与 BI 工具集成，请使用正确的形式！如果你想换种方式来使用扰流板，请跳到文章的结尾，因为我现在要介绍一个用 Python 构建的非常酷的 API 的其馀部分-我...。对了，Python 是生命，比 r 更好！哈哈哈哈哈哈

### 前夜宁静

[Eve](https://docs.mongodb.com/ecosystem/tools/http-interfaces/#rest-interfaces) 是用 Python 编写的 REST API，有几种可用的方法，并且与 Python 2.6、2.7、3.3 和 3.6 兼容。

#### O básico

等我们了解夏娃的故事后我们就知道怎么用她了 well，after we know eve ' s story，we ' ll see how we ' ll use her .本教程可以直接在文档中找到，但我要在此提出几点意见。

Eve 的安装非常简单:pip3 install eve(查看此版本)

创建名为 run.py 的文件并复制以下内容:

```
from eve import Eve
app = Eve()
if __name__ == '__main__':
    app.run() 
```

Enter fullscreen mode Exit fullscreen mode

现在，创建另一个名为 settings.py 的文件，保留在 run.py 文件所在的目录中。这是一个遵循 Python 模块标准的配置文件，它会告诉 Eve 您的 API 只能访问一个资源:people。

```
DOMAIN = { ‘people’: {}} 
```

Enter fullscreen mode Exit fullscreen mode

现在只需运行您的 API:

```
python3 run.py 
```

Enter fullscreen mode Exit fullscreen mode

e para consumer:

```
curl -i http://127.0.0.1:5000 
```

Enter fullscreen mode Exit fullscreen mode

完成后，您已经有一个 GET 请求，答案如下:

默认情况下，您的 API 是只读的，例如，运行一个 DELETE 请求，然后查看答案:

```
curl -X DELETE http://127.0.0.1:5000/people 
```

Enter fullscreen mode Exit fullscreen mode

#### 连接蒙戈布

在 settings.py 文件中，添加以下行:

```
MONGO_HOST = ‘localhost’
MONGO_PORT = 27017

# Se você não tem autenticação habilitada na sua instância, ignore o bloco abaixo (mas é altamente recomendável o uso de autenticação) MONGO_USERNAME = ‘<seu_usuario>’
MONGO_PASSWORD = ‘<sua_senha>’
#Nome do seu database de autenticação MONGO_AUTH_SOURCE = ‘<dbname>’

#Nome do banco de dados onde você quer se conectar MONGO_DBNAME = ‘<nome_do_database>’ 
```

Enter fullscreen mode Exit fullscreen mode

按照蒙戈布的休闲原则，无需创建数据库或集合。如果在空数据库或不存在的数据库中请求 GET，您将收到 200 OK 响应，其他方法(如 DELETE/PATCH/PUT)将收到 404 Not Found，此时 POST 将根据上下文创建数据库和集合。

如我所说，默认情况下，您的 API 是只读的，因此我们将为 CRUD 启用所有方法:

```
RESOURCE_METHODS = [‘GET’, ‘POST’, ‘DELETE’]
ITEM_METHODS = [‘GET’, ‘PATCH’, ‘PUT’, ‘DELETE’] 
```

Enter fullscreen mode Exit fullscreen mode

RESOURCE_METHODS 相当于我要为端点(/people)保留的所有方法，而 ITEM_METHODS 相当于端点和项目(/people/)。这两个设置都是全局设置，适用于所有端点，但您可以在端点级别单独启用或禁用 HTTP 方法。

方法发布后，我们还可以为我们的人员资源创建一个架构。将这些行添加到 settings . py:t0]文件中

```
schema = {
    # baseado no Cerberus grammar. Dê uma olhada no Cerberus project
    # (https://github.com/pyeve/cerberus) pra maiores detalhes.
    'firstname': {
        'type': 'string',
        'minlength': 1,
        'maxlength': 10,
    },
    'lastname': {
        'type': 'string',
        'minlength': 1,
        'maxlength': 15,
        'required': True,
        'unique': True,
    },
    'role': {
        'type': 'list',
        'allowed': ["author", "contributor", "copy"],
    },
    'location': {
        'type': 'dict',
        'schema': {
            'address': {'type': 'string'},
            'city': {'type': 'string'}
        },
    },
    'born': {
        'type': 'datetime',
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

有关模式验证的更多详情在此处。

现在，让我们自定义对 people endpoint 的访问:

*   将项目名称更改为 person
*   在/people/
*   复盖高速缓存控制指令
*   禁用 endpoint /people 的 DELETE 方法(我们将在以后的端点上保留启用)

将这些行添加到 settings . py:t0]文件中

```
people = {
   'item_title': 'person',
    'additional_lookup': {
        'url': 'regex("[\w]+")',
        'field': 'lastname'
    },
    'cache_control': 'max-age=10,must-revalidate',
    'cache_expires': 10,
    'resource_methods': ['GET', 'POST'],
    'schema': schema
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，在 settings.py 中，我们将更新 people 域:

```
DOMAIN = {
  'people': people,
} 
```

Enter fullscreen mode Exit fullscreen mode

保存更改，然后再次运行 run.py .并在 people 终端中插入一些文档。

插入后，我们可以通过 endpoint /people/lastname:
给出 GET 并检索文档

```
$ curl -d '[{"firstname": "barack", "lastname": "obama"}, {"firstname": "mitt", "lastname": "romney"}]' -H 'Content-Type: application/json'  http://127.0.0.1:5000/people 
```

Enter fullscreen mode Exit fullscreen mode

很酷吧？我们可以快速地通过 REST API 公开数据，只需几行代码。显然，应该注意和配置更高级的安全功能，但回到文章的开头:这是一种让需要快速轻松地交换数据的开发人员更轻松的方法！

### 但是你答应的扰流板呢？

-是啊-我...。我差点忘了！一个非常有趣的工具也使得 mongodb 群集与其 BI 工具之间的桥梁不止一个:即排水！因为它只是一个扰流板，所以我不会在此详述，但我保证它是一个非常酷的多功能工具，而且它还与 BI 的主要市场参与者进行了本机集成。等我们写完关于 BI Connector 的系列后我再写！

阿泰迈斯！；)