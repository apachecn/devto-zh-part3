# 使用 Masonite 作为微服务

> 原文：<https://dev.to/rf_schubert/using-masonite-as-microservice-4bml>

我们有一个建立在`Django`之上的大项目，现在我们开始将它分解成几个小部分，以便于维护、更新和扩展。

所以我们决定用`Masonite`构建一些微服务，这是一个很好的 python 框架，几乎内置了我们需要的所有东西，随时可以使用。

那么，如何整合它，而不需要改变我们当前的逻辑呢？

我们决定基本上将其作为 Rest API 使用，并构建了一个小型 SDK 来覆盖我们的代码。

对于样品:

```
class User(models.Model):
    # ...
    def get_user_documents(self):
        from documents.model import Document
        return Document.objects.filter(owner=self) 
```

我们知道上面的关系可以通过`related_name`达到，但是由于我们有很多实现，它在循环导入的一些情况下结束，所以在这个例子中，我们将使用这种方法。

因此，我们将用户的所有文档转移到一个新的微服务，因为在那里我们可以根据需要扩展文档服务器，并且在不需要更新核心服务器的情况下对其进行改进。

我们如何改变上面的类？

```
class User(models.Model):
    # ...
    def get_user_documents(self):
        return DocumentsMS().get_user_documents(user_id=self.id) 
```

在我们的`DocumentMS`类中，我们有几个使用 REST API 的类，例如:

```
import requests
import json

class DocumentMS:
    def get_user_documents(user_id: int):
        url = "https://......"
        headers = {} # security token header
        querystring = {
            "user_id": user_id
        }

        response = requests.request("GET", url, headers=headers, params=querystring)
        return json.loads(response.text) 
```

我们刚刚编写的所有代码都是出于示例目的，为您提供了一种简单的方式来使用微服务和核心服务器，而无需更改您的逻辑。

我们通常在所有实现中使用这种方法，然后根据每种情况的需要深入研究。

我希望这能让你知道如何以一种简单的方式使用微服务。

在下一篇文章中，我将展示如何创建一个小 API 来响应我们刚刚编写的调用。