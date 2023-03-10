# fastapi python 框架简介

> 原文：<https://dev.to/errietta/introduction-to-the-fastapi-python-framework-2n10>

我最近一直在开发一个新的基于 python 的 API，在一位同事的建议下，我们决定使用 [fastapi](//fastapi.tiangolo.com/features/) 作为我们的框架。

Fastapi 是一个基于 python 的框架，它鼓励使用 Pydantic 和 OpenAPI(以前的 Swagger)进行文档编制，使用 Docker 进行快速开发和部署，并由于它所基于的 Starlette 框架而易于测试。

它提供了许多好东西，比如自动 OpenAPI 验证和文档，而没有增加不必要的臃肿负载。在我看来，这是不提供任何内置功能和提供太多内置功能之间的一个很好的平衡。

## 入门指南

安装 fastapi 和一个 ASGI 服务器，比如 uvicorn:

***确保你用的是 python 3 . 6 . 7+；如果**`**pip**`**`**python**`**给你一个版本的 python 2 你可能要用** `**pip3**` **和** `**python3**` **。或者** [**看看我的 python 入门帖子。**](//www.errietta.me/blog/installing-getting-started-python/)**

```
pip install fastapi uvicorn
```

并在`main.py`文件中添加老歌“hello world ”:

```
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def home():
    return {"Hello": "World"} 
```

### 为发展而奔跑

然后要运行开发，可以运行`uvicorn main:app --reload`

对于一个简单的服务器来说，这就是你要做的一切！现在可以勾选 [//localhost:8000/](//localhost:8000/%20) 查看“主页”。此外，正如您所看到的，JSON 的响应是“正常工作”！你还可以在[//localhost:8000/docs](//localhost:8000/docs)‘免费’获得 Swagger UI。

## 确认

如上所述，验证数据很容易(并为接受的数据格式生成 Swagger 文档)。只需从 fastapi 添加`Query`导入，然后用它来强制验证:

```
from fastapi import FastAPI, Query

@app.get('/user')
async def user(
    *,
    user_id: int = Query(..., title="The ID of the user to get", gt=0)
):
  return { 'user_id': user_id }
```

第一个参数`...`是默认值，如果用户没有提供值，则提供该值。如果设置为`None`，则没有默认值，该参数是可选的。为了没有默认值并且参数是强制性的，使用[省略号](//docs.python.org/3/library/constants.html#Ellipsis)或`...`代替。

如果您运行这段代码，您将自动在 swagger UI 上看到更新:

<figure>[![](img/bafef3fffe911d401996077c59a0d714.png)](//www.errietta.me/blog/wp-content/uploads/2019/05/Screenshot-2019-05-21-at-12.59.30.png) 

<figcaption>Swagger UI 允许您查看新的/用户路线，并使用特定的用户 id</figcaption>

</figure>

请求它

如果你输入任何用户 id，你会看到它自动为你执行请求，例如 [//localhost:8000/user？user_id=1](//localhost:8000/user?user_id=1) 。在页面中，您可以看到回显的用户 id！

如果你想使用路径参数(所以它是`/user/1`)，你所要做的就是导入并使用`Path`而不是`Query`。你也可以把两者结合起来

### 发布路线

如果您有一条`POST`路线，您只需像这样定义输入

```
@app.post('/user/update')
async def update_user(
    *,
    user_id: int,
    really_update: int = Query(...)
):
    pass
```

在这种情况下，你可以看到`user_id`被定义为一个没有`Query`或`Path`的整数；这意味着它将出现在 POST 请求正文中。如果你接受更复杂的数据结构，比如 JSON 数据，你应该研究一下[请求模型](//fastapi.tiangolo.com/tutorial/body/#import-pydantics-basemodel)。

## 请求和响应模型

您可以使用 Pydantic 模型详细记录和声明请求和响应模型。这不仅允许您拥有所有模型的自动 OpenAPI 文档，还可以验证请求和响应模型，以确保任何进入的 POST 数据都是正确的，并且返回的数据符合模型。

像这样简单地声明您的模型:

```
from pydantic import BaseModel

class User(BaseModel):
    id:: int
    name: str
    email: str
```

然后，如果您想要将用户模型作为输入，您可以这样做:

```
async def update_user(*, user: User):
    pass
```

或者如果您想将其用作输出:

```
@app.get('/user')
async def user(
    *,
    user_id: int = Query(..., title="The ID of the user to get", gt=0),
    response_model=User
):
  my_user = get_user(user_id)
  return my_user 
```

## 路由和分解更大的 API

您可以使用`APIRouter`将您的 api 分成几个路径。例如，我在我的 API `app/routers/v1/__init__.py`中有这个

```
from fastapi import APIRouter
from .user import router as user_router

router = APIRouter()

router.include_router(
    user_router,
    prefix='/user',
    tags=['users'],
) 
```

然后你可以在`app/routers/v1/user.py`中使用上面的用户代码——只需导入`APIRouter`并使用`@router.get('/')`代替`@app.get('/user')`。它会自动路由到`/user/`，因为该路由是相对于`prefix`的。

```
from fastapi import APIRouter

router = APIRouter()

@router.get('/')
async def user(
    *,
    user_id: int = Query(..., title="The ID of the user to get", gt=0),
    response_model=User
):
  my_user = get_user(user_id)
  return my_user 
```

最后，要在您的应用中使用所有的`v1`路由器，只需将`main.py`编辑为:

```
from fastapi import FastAPI
from app.routers import v1

app = FastAPI()

app.include_router(
    v1.router,
    prefix="/api/v1"
)
```

你可以用这种方式把路由器链接起来，这样你就可以分解大的应用程序并拥有版本化的 API

## 对接和部署

fastapi 的作者让 Dockerizing 变得异常简单。默认`Dockerfile`是 2 行！

```
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.7

COPY ./app /app 
```

想通过自动重新加载进行 dockerize 开发吗？这是我在撰写文件中使用的秘方:

```
version: "3"
services:
  test-api:
    build: ..
    entrypoint: '/start-reload.sh'
    ports:
        - 8080:80
    volumes:
        - ./:/app 
```

这将把当前目录挂载为`app`，并在任何更改时自动重新加载。对于更大的应用程序，你可能也想使用`app/app`。

## 有用的链接

所有这些信息都来自 fastapi 网站，那里有很棒的文档，我鼓励你去阅读。此外，作者在 [Gitter](//gitter.im/tiangolo/fastapi) 上也非常积极和乐于助人！

## 结论

现在就这样——我希望这个指南对您有所帮助，并且您和我一样喜欢使用 fastapi。**