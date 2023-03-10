# 使用 Python NLTK 将文本转换成地图

> 原文：<https://dev.to/j12y/turn-text-into-here-maps-with-python-nltk-541e>

最初发表于[developer.here.com](https://developer.here.com/blog/turn-text-into-here-maps-with-python-nltk)，所以也可以看看原文。

你最想去的五个旅游目的地是哪里？最近，我问了我 8 岁的儿子这个问题，他的回答让我大吃一惊。答案很具体，也很陌生。

[![Venice Mdina Aswan Soro Gryfino](img/504150acfaf4ea729a592ec0d92c52be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_apOGOb9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AIYkXIqjHUzbRZWCzvxlH_A.jpeg)

好吧，我知道威尼斯，但我是北加州的一名软件工程师，很久没学过地理了。我不得不承认我不知道那些地方在哪里。

也许你已经发现自己在阅读一篇旅游文章，这篇文章漂亮地描述了一个迷人的地方，住宿的地方，旅游的地方，和吃饭的地方。这些文章可能会很精彩，但一张简单的地图可以大大增加地方的背景，这是一些专业制作的散文所不能做到的。如果作者或出版商没有为您提供地图，并且您家中没有 8 岁的地理专家，Python 可以提供帮助。

# 解

为了解决这个问题，我们将建立一个 Python Flask 服务器，它向

1.  下载一个给定的 URL 并用 BeautifulSoup 解析 HTML。
2.  使用自然语言工具包(NLTK)根据一些线索从文本中提取位置。
3.  使用 HERE 地理编码器 API 对位置进行地理编码，以确定纬度和经度。
4.  使用 HERE Map Image API 在地图上放置标记来识别已识别的地点。

[![text - download - extract - geocode - map](img/a47c27fd44af8d696d07df8924ec587b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y1BpiDFn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/2174253/text-to-map.png)

# 服务器设置

对于本节，我假设您正在运行一个像 OSX 或 Linux 这样的环境。如果你运行的是 Windows，你需要稍微调整一些命令。

## 配置

有了[的十二因素应用](https://12factor.net/)，最佳实践就是[将配置存储在环境](https://12factor.net/config)中。我同意并喜欢将我的 API 凭证存储在名为【T6 here . sh 的文件中的变量`APP_ID_HERE`和`APP_CODE_HERE`中。

```
#!/bin/bash
export APP_ID_HERE=your-app-id-here
export APP_CODE_HERE=your-app-code-here 
```

我用`. HERE.sh`将它放入我的环境中，以避免任何硬编码的凭证意外地随我的源一起发布。

## 结构

web 服务器组件将需要几个文件，您可以在下面的列表中看到这些文件。通过运行`mkdir app/api_1_0`开始。

```
├── app
│   ├── __init__.py
│   └── api_1_0
│       ├── __init__.py
│       ├── demo.py
│       ├── health.py
├── HERE.sh
├── manage.py
├── config.py
└── requirements.txt 
```

如果你没有使用 Python 的虚拟环境，你应该使用。你可以从 [Hitchiker 的 Python 指南](https://docs.python-guide.org/starting/installation/)中找到更多信息，以便有一个正确的开始。如果 **requirements.txt** 包含以下依赖项，您将需要用 requirements.txt 中的库来初始化您的环境，这可以用`pip install -r requirements.txt`来完成。

```
Flask
Flask-Script
gunicorn
nltk
requests 
```

## App

我们需要 **manage.py** 作为应用程序的主入口点。它看起来像下面的清单:

```
import os
import app
from flask_script import Manager, Server

app = app.create_app('default')
manager = Manager(app)

if __name__ == '__main__':
  port = os.environ('PORT', 8000)
  manager.add_command('runserver', Server(port=port))
  manager.run() 
```

为了简洁起见，我省略了一些细节，比如记录和打印 URL。这对于我们的任务来说并不特别有趣，只是为我们的 API 运行一个简单的服务器的一些日常工作。

**config.py** 对于引入我们稍后需要引用的一些环境变量也很重要。

```
import os

class Config(object):
  SECRET_KEY = os.environ.get('FLASK_SECRET_KEY')
  APP_ID_HERE = os.environ.get('APP_ID_HERE')
  APP_CODE_HERE = os.environ.get('APP_CODE_HERE')

  @staticmethod
  def init_app(app):
    pass

config = {'default': Config} 
```

与其他 Python 项目不同，我们的 init 文件在这个项目中非常重要。在 **app/ **init** 中。我们定义了在 **manage.py** 中看到的`create_app`函数。** 

```
from config import config
from flask import Flask

def create_app(config_name):
  app = Flask(__name__)
  app.config.from_object(config[config_name])
  config[config_name].init_app(app)

  from .api_1_0 import api as api_1_0_blueprint
  app.register_blueprint(api_1_0_blueprint, url_prefix='/api/1.0')

  return app 
```

这为我们的 api 中的任何资源提供了清晰的 API 版本控制。我们还需要定义 **app/api_1_0/ **init** 。py** 带一些配置

```
from flask import Blueprint
api = Blueprint('api', __name__)

from . import health
from . import demo 
```

如您所见，我们确实需要确保我们创建的每个库都被标识为蓝图的一部分。

## 健康检查

为了确保我们的服务器正常运行，我们可以在文件**app/API _ 1 _ 0/health check . py**中添加一个快速健康检查端点。

```
from flask import jsonify
from flask import current_app as app
from . import api

@api.route('/health', methods=['GET'])
def handle_health():
  return jsonify({
    'hello': 'world',
    'app_id_here': app.config['APP_ID_HERE'],
    'app_code_here': app.config['APP_CODE_HERE']
    }) 
```

在这一点上，我们应该能够运行`python manage.py runserver`并有生命的证据。如果您使用浏览器访问[http://localhost:8000/health check](http://localhost:8000/healthcheck)，我们应该会得到一个响应，确认我们的服务器已经启动，并且已经正确配置了我们的 app_id 和 app_code。

一旦你进入生产阶段，你可能不想展示这个，但是当我们处于“hello world”阶段时，这是很好的。

# 文本

出于开始的目的，我将使用一个简单的文本文件，其中只包含我们之前的位置。

```
Mdina
Aswan
Soro
Gryfino
Venice 
```

对于要测试的更复杂的数据集，我建议尝试一下纽约时报、T2、华尔街日报、T4、英国广播公司的旅游栏目。

# 提取

我们需要从 HTML 中提取文本，并对找到的任何可能是位置的单词进行标记。我们将定义一个方法来处理对资源**/令牌**的请求，以便我们可以独立地查看每个步骤。

```
@api.route('/tokens', methods=['GET'])
def handle_tokenize():
  # Take URL as input and fetch the body
  url = request.args.get('url')
  response = session.get('url')

  # Parse HTML from the given URL
  body = BeautifulSoup(response.content, 'html.parser')

  # Remove JavaScript and CSS from our life
  for script in body(['script', 'style']):
    script.decompose()

  text = body.get_text()

  # Ignore punctuation
  tokenizer = RegexpTokenizer(r'\w+')

  # Ignore duplicates
  tokens = set(tokenizer.tokenize(text))

  # Remove any stop words
  stop_words_set = set(stopwords.words())
  tokens = [w for w in tokens if not w in stop_words_set]

  # Now just get proper nouns
  tagged = pos_tag(tokens)
  tokens = [w for w,pos in tagged if pos in ['NNP', 'NNPS']]

  return jsonify(list(tokens)) 
```

在此工作之前，我们需要下载 NLTK 资源。这是一个一次性操作，您可以在交互式 python shell 中或通过执行简单的脚本来完成。

```
$ python
...
>>> import nltk
>>> nltk.download('stopwords')
>>> nltk.download('averaged_perceptron_tagger') 
```

有了这个 **demo.py** 之后，我们可以重启我们的服务器，并调用下面的端点来获取可能是位置的任何术语的列表。

```
#!/bin/bash
curl http://localhost:8000/api/1.0/tokens?url=$1 
```

这将从一篇示例文章中返回一个类似于:
的响应

```
["Canada", "Many", "Conners", "Kelly", "Americans", "Biodinamica", "Milan", "Fabio", ... ] 
```

我们已经从原始文章中大幅削减了字数，但仍有一些工作可以做，以微调这一识别过程。这对于第一遍来说已经足够好了，但不会增加更多的复杂性，所以让我们看看是否可以开始用地理编码器识别这些地方。

# 地理编码

HERE Geocoder API 非常简单地将人类可以理解的位置转换成地理坐标。如果你输入一个地址，就会得到经度和纬度。

下面是一个地理编码器端点的列表:

```
@api.route('/geocode', methods=['GET'])
def handle_geocode():
  uri = 'https://geocoder.api.here.com/6.2/geocode.json'
  headers = {}
  params = {
    'app_id': app.config['APP_ID_HERE'],
    'app_code': app.config['APP_CODE_HERE'],
    'searchtext': request.args.get('searchtext')
  }

  response = session.get(uri, headers=headers, params=params)
  return jsonify(response.json()) 
```

重启 python 服务器，发送一个请求，请求一个城市，比如“gry fino”:

```
#!/bin/bash
curl http://localhost:8000/api/1.0/geocode?searchtext=$1 
```

该响应包括我可能放置标记以在地图上显示该位置的位置。

```
"DisplayPosition": {
  "Latitude": 53.25676,
  "Longitude": 14.48947
}, 
```

# 地图

最后，我们将从我们的
地理编码请求中获取纬度和经度，并使用 HERE 地图图像 API 生成一个简单的渲染。

这个清单如下所示:

```
@api.route('/mapview', methods=['GET'])
def handle_mapview():
  uri = 'https://image.maps.api.here.com/mia/1.6/mapview'
  headers = {}
  params = {
    'app_id': app.config['APP_ID_HERE'],
    'app_code': app.config['APP_CODE_HERE'],
    'poi': request.args.get('poi')
  }

  response = session.get(uri, headers=headers, params=params)
  image_path = tempfile.mktemp()
  open(image_path, 'wb').write(response.content)

  return image_path 
```

为了简洁起见，我没有包括您应该在这里做的任何错误/响应处理。为了便于说明，我还通过将图像存储到本地文件系统中来欺骗了一下。

现在，通过用逗号分隔的纬度、经度对列表调用这个端点，它将返回一个地图，其中所有位置都有标记。

[![Map with Points Placed](img/00698b0c70cf611ce869f9fc914804db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vx8m-jsa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/2174253/mapview.png)

没有附加上下文的地名可能会有歧义，因此在某些情况下会有多个匹配项。这张地图只显示了第一场比赛，尽管威尼斯海滩可能会很有趣。

# 总结

将`/tokens`、`/geocode`和`/mapview`作为独立端点的原因是，这说明了如何使用 Python + Flask 为每个想要执行的操作设置微服务。这将允许部署独立地扩展它们。

您可以在 GitHub 项目中找到完整的源代码清单。

对于额外的初始剂量，尝试运行服务器并处理本文本身。