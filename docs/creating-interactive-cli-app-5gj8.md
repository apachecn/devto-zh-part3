# 创建交互式 CLI 应用程序

> 原文：<https://dev.to/mraza007/creating-interactive-cli-app-5gj8>

在这篇博文中，我将带你完成使用 Python 编写交互式命令行应用程序的过程。我是命令行应用程序的超级粉丝，我喜欢创建命令行应用程序，因为它有助于我保持工作效率，并使导航比 GUI 容易得多。

无论如何，我们将创建一个简单的命令行应用程序，使用 Yelp Api 找到你在你的城市最喜欢的餐馆。

### 我们开始吧

*   为了开始，请确保您已经安装了`python3`。
*   现在我们将使用`pipenv`创建一个虚拟环境来处理我们所有的 python 依赖项。

```
pipenv shell 
```

如果你没有安装 pipenv，你可以[点击这里](https://pipenv.readthedocs.io/en/latest/)

*   激活外壳后，我们现在要安装我们的软件包。对于这个命令行应用程序，我将使用 [PyInquirer](https://github.com/CITGuru/PyInquirer) 。这是一个非常酷的软件包，可以帮助你构建交互式命令行应用。

```
pipenv install PyInquirer 
```

```
pipenv install prompt_toolkit==1.0.14 
```

*   一旦我们安装了`PyInquirer`，现在我们将安装 yelp fusion api `pipenv install yelpapi`的 python 实现。
    [YelpApi(Python 实现)](https://github.com/gfairchild/yelpapi)

*   最后，我们准备好了一切。

### 我们来写程序吧。

```
from __future__ import print_function, unicode_literals
import os
import emoji
from PyInquirer import style_from_dict, Token, prompt, Separator
from examples import custom_style_2
import json
from termcolor import colored
from yelpapi import YelpAPI
# Api key from Yelp Developers api_key = os.environ['API_KEY']
## This allows you create prompts for the user. questions = [
    {
        'type': 'input',
        'name': 'food',
        'message': emoji.emojize('What are you looking to eat ? :sushi:,:pizza:,:hamburger:'),

    },
    ## Allows you have a list of choices 
    ## Furthermore you can refer to the documentation.
    {
        'type': 'list',
        'name': 'city',
        'message': 'Choose your City',
        'choices': [
                "New York City",
                "Los Angeles",
                "Chicago",
                "Houston",
                "Philadelphia",
                "Phoenix",
                "San Antonio",
                "San Diego",
                "Dallas",
                "San Jose",
                "Austin",
                "Jacksonville",
                "San Francisco",
                "Indianapolis",
                "Columbus",
                "Fort Worth",
                "Charlotte",
                "Detroit",
                "El Paso",
                "Seattle ",
                "Denver ",
                "Washington",
                "Memphis",
                "Boston ",
                "Nashville-Davidson",
                "Baltimore",
                "Not Listed"
        ]
    },

]
## This will allow you get the results from Yelp Api based on user's input. answers = prompt(questions, style=custom_style_2)
if answers['city'] == 'Not Listed':
    x = input("What\'s your city: ")
    search_results = yelp_api.search_query(
        term=answers['food'],
        location=answers['city'],
        sort_by='rating',
        limit=5)
    print('Here are few restaurants I found ')
    for nums in search_results['businesses']:
            print('Name : ' +
                  colored(nums['name'], 'white', attrs=['bold']) +
                  '\n' +
                  'Phone: ' +
                  colored(nums['phone'], 'green', attrs=['bold']) +
                  '\n' +
                  'Address: ' +
                  colored(nums['location']['address1'],'cyan',attrs=['bold']) +
                  '\n' +
                  colored('Yelp url: ','red',attrs=['bold']) +
                  nums['url']
                  )
else:
    search_results = yelp_api.search_query(
        term=answers['food'],
        location=answers['city'],
        sort_by='rating',
        limit=5)
    print('Here are few restaurants I found ')
    for nums in search_results['businesses']:
            print('Name : ' +
                  colored(nums['name'], 'white', attrs=['bold']) +
                  '\n' +
                  'Phone: ' +
                  colored(nums['phone'], 'green', attrs=['bold']) +
                  '\n' +
                  'Address: ' +
                  colored(nums['location']['address1'],'cyan',attrs=['bold']) +
                  '\n' +
                  colored('Yelp url: ','red',attrs=['bold']) +
                  nums['url']
) 
```

*   最后，为了运行这个程序，我们需要从 Yelp 开发者那里获得 api 密匙。
*   一旦你有了 api 密匙，现在你需要导出那些密匙`export API_KEY=XXXXXXX`
*   现在一切就绪，猜猜你写了什么你的第一个交互式 CLI 应用。

不管怎样，这是我在忙于学业很长一段时间后的第一篇博文。我希望你喜欢阅读它，如果你有空的话，可以查看我的邮件列表，订阅 T2，如果你认为我错过了什么，欢迎在下面评论。

感觉在你的朋友和同事之间分享它。

#### [源代码](https://github.com/mraza007/yelp-cli)