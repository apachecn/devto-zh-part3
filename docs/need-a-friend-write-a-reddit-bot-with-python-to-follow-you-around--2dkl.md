# 需要朋友吗？用 Python 写一个 Reddit 机器人跟着你到处跑🤖

> 原文：<https://dev.to/healeycodes/need-a-friend-write-a-reddit-bot-with-python-to-follow-you-around--2dkl>

我们将编写一个 Reddit 机器人来监听特定的短语，并对包含这些短语的评论做出响应。我们将学习如何在 Python 中与 Reddit API 交互，同时创建我们自己的机器人伙伴。

#### 瓶子

你可能听说过机器人三定律，但是你听说过 Reddit 的 T2 瓶礼仪吗？这是一个规则列表，它将帮助你创建一个不会妨碍任何人的礼貌机器人。最重要的规则是不要用你的机器人冒充人类。虽然可以上下投票，但这样做的命令必须直接来自人类。

#### 设置

你将需要 [PRAW](https://praw.readthedocs.io/en/latest/index.html) ，*Python Reddit API 包装器*。使用`pip install praw`安装此软件包。它有大量的文档，如果你需要更多的灵感或帮助，还有一个 [r/redditdev](https://www.reddit.com/r/redditdev/) 子编辑。

创建一个**脚本** [Reddit app](https://www.reddit.com/prefs/apps/) ，记下客户端 id 和客户端密码。

[![](img/294ca9246765f23fc0d1f3d4c07a7b92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lKJCl_hH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i8ssgy49eobulnuzgfxm.png)

确保不要将这些提交给存储库！使用 Python，可以用映射对象`os.environ`访问环境变量。下面是一个你好世界 PRAW 应用程序。由于我们的机器人将执行帐户操作，我们还需要帐户的用户名和密码。对于被动机器人，这是不必要的。还有[其他方式来认证](https://praw.readthedocs.io/en/latest/getting_started/authentication.html)。

```
# Setting environment variables on different platforms:
Linux/OS X  - export ENV_VAR='data'
Windows Cmd - ENV_VAR='data'
Powershell  - $env:ENV_VAR='data' 
```

```
import os
import praw

# a reddit instance reddit = praw.Reddit(client_id=os.environ['CLIENTID'],
                     client_secret=os.environ['CLIENTSECRET'],
                     user_agent='Reddit bot tutorial v0.1-1',
                     username=os.environ['CLIENTUSER'],
                     password=os.environ['CLIENTPASS'])

# with incorrect authentication information, this will error with:
#   prawcore.exceptions.OAuthException: invalid_grant error processing request print(reddit.user.me()) 
```

因为我们的敏感信息是在别处定义的，所以我们可以在不泄露任何细节的情况下提交它。它也更容易测试，并让其他开发人员更容易使用这些代码。

#### 所有评论

这是一件有趣的事情。我对你的挑战是:试着阅读发布到 Reddit 上的每一条评论。这真的让你了解了可用数据的规模。

```
# with our reddit instance, get an endless stream of comments for comment in reddit.subreddit('all').stream.comments():
    print(comment.body) 
```

这里，`comments`是一个生成器函数，当新的注释可用时，它会生成新的注释。与大多数 API 一样，Reddit API 也有速率限制。幸运的是，PRAW 在后台为我们管理这些，并控制我们。

我们想要过滤特定短语的评论。按照与环境变量相同的方式，我们也将泛化这个特性。 [argparse](https://docs.python.org/3/library/argparse.html) ，Python 标准库的一部分，允许命令行参数轻松配置。

```
import argparse

# setup argparse parser = argparse.ArgumentParser(description='Reply to certain Reddit comments')
parser.add_argument('--search', dest='search',
                    help='Search for these comma-delimited phrases. E.g., "one phrase, another phrase"')
args = parser.parse_args()

# split by commas, strip extra white space search = [phrase.strip() for phrase in args.search.split(',')]

# python args.py --search "test phrase, two, three"
# prints: ['test phrase', 'one', 'two'] print(search) 
```

#### 回复评论

Reddit 的 [r/test](https://www.reddit.com/r/test/) 是检验这一点的好地方。我们就用[评论#回复](https://praw.readthedocs.io/en/latest/code_overview/models/comment.html#praw.models.Comment.reply)。因为我们将在注释体中搜索我们的短语，我们已经有了一个对注释对象的引用，我们可以简单地使用它的`reply`方法。否则，您可以找到创建一个带有 id 的注释对象。

```
# with a comment reference comment.reply('hello! signed, a bot.')

# find a comment by id comment = reddit.comment(id='dxolpyc')
comment.reply('hi again!') 
```

你可能需要两个帐户来正确地测试这一点，因为新创建的 Reddit 帐户不能立即发布两个评论，即发布一个目标短语，然后发布对该评论的回复。

#### 我们到目前为止在哪里

让我们看看我们的机器人进展如何。你会看到我使用了`argparse`来概括搜索短语。我们正在制作一个可配置的机器人，而不是一个将来难以扩展和使用的硬编码机器人。

```
import os
import praw
import argparse

# setup argparse parser = argparse.ArgumentParser(description='Reply to certain Reddit comments')
parser.add_argument('--search', dest='search',
                    help='Search for these comma-delimited phrases. E.g., "one phrase, another phrase"')
parser.add_argument('--reply', dest='reply',
                    help='Reply to target comments with this phrase')
args = parser.parse_args()

# our search phrases, also works with a single word search = [phrase.strip() for phrase in args.search.split(',')]
# our reply reply = args.reply

# reddit instance reddit = praw.Reddit(client_id=os.environ['CLIENTID'],
                     client_secret=os.environ['CLIENTSECRET'],
                     user_agent='Reddit bot tutorial v0.1-1',
                     username=os.environ['CLIENTUSER'],
                     password=os.environ['CLIENTPASS'])

# an endless stream of comments for comment in reddit.subreddit('all').stream.comments():
    for phrase in search:
        if phrase in comment.body:
            comment.reply(reply)
            # for debugging
            print(f'Replied to {comment.id}!')
            break 
```

#### 何去何从

*   克隆[教程库](https://github.com/healeycodes/Reddit-Bot-Tutorial)获得黑客攻击！
*   获取发帖人的名字并在回复中使用:`comment.author.name`
*   将你的机器人放在一个树莓平台上，或者和 Heroku 一起放在云端。
*   将你的机器人连接到一个后端服务，就像我对我的表情流项目所做的那样，该项目将发布到 Reddit 的每一个表情流。

#### 更

我最近成为了[编码教练](https://mentors.codingcoach.io/)的导师。如果你正在开发一个 Reddit 机器人，或者需要一般的开发帮助，我可以帮助你！

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。