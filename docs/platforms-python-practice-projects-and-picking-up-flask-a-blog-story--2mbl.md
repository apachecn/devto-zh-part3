# 平台、Python 实践项目和拿起 Flask:一个博客故事

> 原文：<https://dev.to/therealdarkmage/platforms-python-practice-projects-and-picking-up-flask-a-blog-story--2mbl>

# 平台、Python 实践项目和拿起 Flask:一个博客故事

### 警告:使用了原始语言。我有时候嘴巴很脏。如果你不喜欢看到成人语言，就不要阅读。我在这里是原始的，未经过滤的，就像我在现实生活中一样！

### 对度量感兴趣的人注意:sh*t 出现 7 次，f*ck 出现 2 次。

我主要是一名程序员。有人付钱让我开发像 iOS 应用这样的东西。我还被雇来修复大型企业 iOS 应用程序中的错误。我还通过编写 Python 脚本和在 iOS、Android 和 Web 应用程序上执行 pentests 获得报酬。

我的技能和“真正的开发人员”的技能有天壤之别。

真正的开发者建造东西。我只是擅长编码。我建造了一些东西，但不是常规的；也没有真正的开发人员的知识，就像一个计算机科学毕业生，没有明确的方向，只是试图回到他的财务上，这样他就可以搬出他奶奶的家，过上更幸福的生活。

我倾向于选择需要和/或要求我做的事情。我开始意识到一个“开发人员”可以拥有的广泛的工具和角色。外面有很多，如果期望我学习像“React.js”这样的垃圾工具链，这与我保持垃圾最小化的整个方法完全相反，我会被诅咒的。

最近，我申请进入某个网站的平台，在与一名工程师的随意聊天后，我被要求建立一个“小”(对我来说，当时并不小)后端项目。我们给出了一些 REST 端点、关于它应该如何工作的细节(尽管还不够详细)，以及一些驱动事物的输入/输出示例。你会得到一些关于对象设计的提示。如何建设由你决定。

所以，因为我喜欢 Python，并且希望保持事物的“灵活性”，我选择了学习一种叫做“Flask”的东西。

下面是这个项目中基本上杂乱无章的个人笔记。这实际上是我在构建和学习事物时的工作方式。这会让人觉得有点“漫不经心”，因为这是我在工作时反思自己的方式。我通过减价博客和自己来回“聊天”。除了通过文字和手写，我不怎么和自己“交谈”。

为了这个项目，我去了*博客重*。

我已经更改了 REST 端点的名称，并删除了暗示该项目的 URL。我认为期望我不与世界上的其他人分享我的研究和新学到的工具和技能是非常“错误”的，所以我不能被期望那样，所以我已经做了尽职调查来隐藏任何“有意义的”细节。

我期待着将来发表博文，并作为一名 Python 用户提高自己的技能...程序员、开发人员和黑客，并为他人创造东西，这样我就可以得到报酬，进入一个更好的环境，这样我就可以快乐和满足...嗯，更是如此:)

* * *

# xxxxx -发展博客

```
Refresh JWT

POST /some-token-endpoint/refresh
-----
User login

POST /some-token-endpoint
-----
Log out current user

DELETE /some-token-endpoint
-----
Get current user's info

GET /myinfo
-----
Create idea

POST /getideas
-----
Delete idea

DELETE /getideas/:id
-----
Get a page of ideas (1 page = 10 ideas)

GET /getideas
-----
Update idea

PUT /getideas/:id
-----
User Signup

POST /getusers 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这看起来很容易，只是开始是困难的部分。
我们可以使用 flask 来定义每个 API 端点。
我们没有在 API 中列出，但是在`/`有一个隐含的登录页面。

当前日期和时间:**2019 年 2 月 23 日星期六 22:55**
交货日期:**2019 年 3 月 2 日星期六上午 06:35**
当前花费时间:**12 秒 30 秒**
总花费时间:**12 秒 30 秒**

* * *

对于本博客的每个新更新/条目，
我们将在前面加上当前日期/时间，
发送日期，以及当前和总花费时间

* * *

### 对本项目有用的链接

*   **删除保护项目**
*   **删除保护项目**
*   [https://jwt.io/introduction/](https://jwt.io/introduction/)
*   [https://tools.ietf.org/html/rfc7519](https://tools.ietf.org/html/rfc7519)

* * *

当前日期和时间:**2019 年 2 月 25 日星期一 01:33**
交货日期:**2019 年 3 月 2 日星期六 06:35 上午**
当前花费时间: **1hr20m**
总花费时间: **1hr32m**

初始页面已创建。

```
/
/user_registration
/getusers
/some-token-endpoint 
```

Enter fullscreen mode Exit fullscreen mode

待办事宜:

1.  处理用户注册
    *   数据库初始创建
    *   数据库更新
2.  处理用户登录
3.  处理用户注销

* * *

持续的...

不知何故，我的 flask 不再链接到 Python，我安装了太多版本，所以我将花时间清理我的 Python 安装，删除现有的版本，然后通过 brew 从头开始安装。

* * *

当前日期和时间:**2019 年 2 月 25 日星期一 23:46**
交货日期:**2019 年 3 月 2 日星期六上午 06:35**
当前花费时间: **44m01s**
总花费时间: **1hr44m33m**

我修好了烧瓶装置...消耗了一点时间...Python3 也是。

我移动了一些代码，但没有取得多大进展。

* * *

我需要做一些关于如何处理用户注册和如何在数据库中存储数据的研究。

我们现在将在这方面花时间。

*   [https://blog . miguelgrinberg . com/post/the-flask-mega-tutorial-part-ii-templates](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-ii-templates)

所以，模板是个好主意。

我们应该现在就做。

* * *

好了，我们已经成功地平稳过渡到了模板页面系统，用这种方式创建和管理页面感觉很自然。

我们仍然需要创建初始用户表，并决定如何存储每个用户的想法。

```
user table:
user_id - email - password_hash 
```

Enter fullscreen mode Exit fullscreen mode

`user_id`可以链接到其他表格内容，这样我们就可以把想法藏在一个 blob 对象或其他什么东西里。

我不确定做这件事的“最佳”方式是什么，所以我必须研究一下。

当前日期和时间:**2019 年 2 月 26 日星期二 00:14**
交货日期:**2019 年 3 月 2 日星期六上午 06:35**
当前花费时间: **24m08s**
总花费时间: **2hr08m41m**

* * *

已安装`flask-wtf`。

把东西挪来挪去，这样更“合适”。

App 结构:

```
xxxxx/
    config.py
    xxxxx.py
    run.sh

    app/
        __init__.py
        routes.py

        templates/
            index.html
            user_registration.html
            users.html 
```

Enter fullscreen mode Exit fullscreen mode

一切仍然构建和运行良好。

我们仍然需要做用户注册，但我们正在按照教程。

在我们开始之前必须阅读。

时间检查。

当前日期和时间:**星期四 2019 年 2 月 28 日 02:21**
交货日期:**星期六 2019 年 3 月 2 日 06:35 AM**
当前花费时间: **28m11s**
总花费时间: **2hr36m52m**

* * *

更深入地研究了建筑形式。

实际 LoginForm()对象。

当前日期和时间:**星期四 2019 年 2 月 28 日 02:48**
交货日期:**星期六 2019 年 3 月 2 日 06:35 AM**
当前花费时间:**25 分 47 秒**
总花费时间: **3hr01m39m**

* * *

我们已经取得了一些进展，但现在正在实现当用户输入无效的表单数据时的错误消息。

`LoginForm()`和`RegistrationForm()`。

休息一会儿睡觉。

当前日期和时间:**星期四 2019 年 2 月 28 日 03:28**
交货日期:**星期六 2019 年 3 月 2 日 06:35 AM**
当前花费时间: **39m12s**
总花费时间: **3hr40m51m**

* * *

```
My xxxxx:

______________________________
usermode: 10
trial: 10
continental: 10
averaje: 10

OK Cancel 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我最终完成了本教程的数据库部分:

*   [https://blog . miguelgrinberg . com/post/the-flask-mega-tutorial-part-iv-database](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-iv-database)

我创建了一个`User`和`Idea`‘模型’，它基本上是表的定义。

当我们改变时修复数据库:

```
flask db migrate
flask db upgrade 
```

Enter fullscreen mode Exit fullscreen mode

当你进行修改时，你可以评论

```
flask db migrate -m "your comment" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在我们已经完成了，我们准备开始使用登录:

*   [https://blog . miguelgrinberg . com/post/the-flask-mega-tutorial-part-v-user-logins](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-v-user-logins)

我们一直在努力工作，所以应该休息一下。

当前日期和时间:**2019 年 2 月 28 日星期四 22:06**
交货日期:**2019 年 3 月 2 日星期六上午 06:35**
当前花费时间: **1hr42m57s**
总花费时间: **4hr23m48s**

* * *

当前差异:

修复了一堆狗屎，当我在登录教程中工作时，有东西坏了，但结果是这两个导入在另一个文件的下一行引起了错误，当我试图从`__init__.py`导入东西时。

当前日期和时间:**Fri 2019 年 3 月 1 日 02:46**
交货日期:**星期六 2019 年 3 月 2 日 06:35AM**T5】当前花费时间:**1 HR 29m 23s**T8】总花费时间: **5hr53m11s**

我们仍然需要检查 shell 并从该级别登录。

啊狗屎坏了

是

我们起飞了！！！

我已经测试了登录表单，它可以正常工作。

这又有点令人兴奋了！

我们可以休息一下。需要理清思路，为下一步做准备。

当前日期和时间:**Fri 2019 年 3 月 1 日 03:29**
交货日期:**星期六 2019 年 3 月 2 日 06:35 AM**
当前花费时间: **40m48s**
总花费时间: **5hr04m36s**

* * *

后续步骤:

*   用户注册*简单*
*   实现想法
    *   添加一个想法
    *   删除一个想法
    *   展示想法

* * *

好了,
是时候把这狗屎磨出来了！
用户注册！
我们成功了。
您可以立即创建新用户并立即登录。
剩下的就是想法清单了。
让我们检查并继续。

当前日期和时间:**Fri 2019 年 3 月 1 日 23:36**
交货日期:**星期六 2019 年 3 月 2 日 06:35 AM**
当前花费时间: **22m48s**
总花费时间: **5hr28m04s**

* * *

*   实现想法
    *   添加一个想法
    *   删除一个想法
    *   展示想法

* * *

用户注册成功后，我们应该将用户带到“我的想法”页面。

我认为我实际上只需要实现端点，而不是页面本身`-_-`哎呀！

好的...

让我们重新安排一下。

我先做用户注册。

如果我能用旋度测试这个...首先，我需要修复卷曲...酿造卷曲...

好的...

* * *

进度报告:

我让`POST /getusers`反映我们的输入数据。

我们需要它来计算一个`jwt`令牌和一个`refresh_token`，并将其作为 json 返回。

```
{
    "jwt" : "...",
    "refresh_token" : "..."
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们可以用`Flask-JWT`来做这件事。

[https://pythonhosted.org/Flask-JWT/](https://pythonhosted.org/Flask-JWT/)

我已经安装了它，并把它安装到了`routes.py`中，但是当我试图在`/auth`验证时出错...

```
./run.sh           
 * Serving Flask app "xxxxx.py"
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
[2019-03-02 02:56:46,265] ERROR in app: Exception on /auth [POST]
Traceback (most recent call last):
  File "/usr/local/lib/python3.7/site-packages/flask/app.py", line 2292, in wsgi_app
    response = self.full_dispatch_request()
  File "/usr/local/lib/python3.7/site-packages/flask/app.py", line 1815, in full_dispatch_request
    rv = self.handle_user_exception(e)
  File "/usr/local/lib/python3.7/site-packages/flask/app.py", line 1718, in handle_user_exception
    reraise(exc_type, exc_value, tb)
  File "/usr/local/lib/python3.7/site-packages/flask/_compat.py", line 35, in reraise
    raise value
  File "/usr/local/lib/python3.7/site-packages/flask/app.py", line 1813, in full_dispatch_request
    rv = self.dispatch_request()
  File "/usr/local/lib/python3.7/site-packages/flask/app.py", line 1799, in dispatch_request
    return self.view_functions[rule.endpoint](**req.view_args)
  File "/usr/local/lib/python3.7/site-packages/flask_jwt/__init__.py", line 115, in _default_auth_request_handler
    username = data.get(current_app.config.get('JWT_AUTH_USERNAME_KEY'), None)
AttributeError: 'NoneType' object has no attribute 'get'
127.0.0.1 - - [02/Mar/2019 02:56:46] "POST /auth HTTP/1.1" 500 - 
```

Enter fullscreen mode Exit fullscreen mode

这看起来像是 JWT 弗拉斯克的一个可能的错误，但是我在这里不能 100%确定...

因为这整件事花了我很长时间，我已经要求他们再花 4 天时间来完成它。如果我努力工作，我可以在 4 天内完成。

问题是，这部分现在代表了一个路障...

即使我回溯/从头开始，在最少的烧瓶中重新构建一切，当我到达这一部分时，我仍然必须解决如何生成 JWT...

Flask_JWT 应该已经为我解决了这个问题，但即使使用它也有点挑战性...

我吸收了很多新信息，这需要我做一些心理背景处理。

所以，重述一下:

```
/getusers is reflecting our json post data now
/auth is crashing for Flask_JWT 
```

Enter fullscreen mode Exit fullscreen mode

当前日期和时间:**2019 年 3 月 2 日星期六 03:03**
交货日期:**2019 年 3 月 2 日星期六 06:35AM**T5】当前花费时间: **1hr50m01s**
总花费时间: **7hr18m05s**

* * *

# 2019 年 3 月 6 日星期三

更新统计数据:

当前日期和时间:**2019 年 3 月 6 日 14:29**
交货日期:**Fri 2019 年 3 月 8 日 11:59PM**T5】当前花费时间: **0hr0m0s**
总花费时间: **7hr18m05s**

我们获准延期。

看看这个:

我们需要从头开始，我们建设过度了。

```
User Signup: POST /getusers
Refresh JWT: POST /some-token-endpoint/refresh
User login: POST /some-token-endpoint
Log out: DELETE /some-token-endpoint
Get current user's info: GET /myinfo
Create idea: POST /getideas
Delete idea: DELETE /getideas/:id
Get a page of ideas (1 page = 10 ideas): GET /getideas
Update idea: PUT /getideas/:id 
```

Enter fullscreen mode Exit fullscreen mode

对于每种可用的方法:

1.  用户注册
2.  刷新 JWT
3.  用户登录
4.  用户注销
5.  获取当前用户的信息
6.  产生一个想法
7.  删除一个想法
8.  获得一页的想法
9.  更新想法

我们需要重新创建数据库。

```
class User:
    name
    email
    password_hash

class Idea:
    user_id 
    text
    usermode
    trial
    continental
    averaje

UserTable
IdeaTable 
```

Enter fullscreen mode Exit fullscreen mode

* * *

第一次提交。你好世界。

当前日期和时间:**2019 年 3 月 6 日 14:49**
交货日期:**Fri 2019 年 3 月 8 日 11:59PM**T5】当前花费时间: **0hr8m12s**
总花费时间: **7hr26m17s**

* * *

接下来我们需要再次创建数据库和表。

完成了。

当前日期和时间:**2019 年 3 月 6 日 15:19**
交货日期:**Fri 2019 年 3 月 8 日 11:59 PM**
当前花费时间: **0hr23m49s**
总花费时间: **7hr50m06s**

* * *

接下来，实际实现每个功能。
我们逻辑上应该从*用户注册*开始。

**用户注册**

1.  解析出输入
2.  验证输入
3.  将经过验证的输入传递给新的用户对象
4.  将用户提交到数据库
5.  构造 JWT 并返回所需的令牌

验证输入。

当前日期和时间:**2019 年 3 月 6 日 23:05**
交货日期:**Fri 2019 年 3 月 8 日 11:59PM**T5】当前花费时间: **0hr28m0s**
总花费时间: **8hr18m06s**

* * *

第一次听说`flask_jwt`。

*   [https://pythonhosted.org/Flask-JWT/](https://pythonhosted.org/Flask-JWT/)

然后我就听说了`flask_jwt_extended`。

*   [https://flask-jwt-extended . readthedocs . io/en/latest/installation . html](https://flask-jwt-extended.readthedocs.io/en/latest/installation.html)

然后我听说了`flask_jwt_auth`

*   [https://real python . com/token-based-authentic ation-with-flask/](https://realpython.com/token-based-authentication-with-flask/)

所有这些教程都是巨大的痛苦！

* * *

哦，该死的
真他妈的管用
我们正在生成 jwt！！！

魔酱是

```
retval = {
    'access_token' : create_access_token(identity=u.id),
    'refresh_token' : create_refresh_token(identity=u.id)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上同时获得了 JWT 和 refresh_token。

我的主要问题仍然是`refresh_token`首先是如何工作的，这在 JWT 的介绍中没有解释，但我仍然希望在提交时知道那是什么以及它是如何工作的...

哦，对了，有一些像`@refresh_token_required`之类的室内装潢师。

这来自图书馆`flask_jwt_extended`所以谢谢你成为我的救世主。

我们仍然需要实现所有其他的 API，但是`registerUser`已经正式完成了！

* * *

对于每种可用的方法:

**1。用户注册完成**

1.  刷新 JWT
2.  用户登录
3.  用户注销
4.  获取当前用户的信息
5.  产生一个想法
6.  删除一个想法
7.  获得一页的想法
8.  更新想法

当前日期和时间:**星期四 2019 年 3 月 7 日 23:30**
交货日期:**Fri 2019 年 3 月 8 日晚上 11:59**T5】当前花费时间:**1 HR 31 m02s**T8】总花费时间: **9hr49m08s**

* * *

自我提醒:

我们在获取 SECRET_KEY 时遇到这么多麻烦的原因是，我们从一个名为 Config 的已定义对象中配置我们的应用程序。为了解决这个问题，我们必须将 SECRET_KEY 和 JWT_SECRET_KEY 移到类定义中。

* * *

让我们登录。那应该很容易。

```
Mar 8 1:57am: TIL Flask routes expect to return Responses or strings, tuples, etc, but not Dicts! 
```

Enter fullscreen mode Exit fullscreen mode

看起来登录完成了。

我们可以花几分钟来清理调试消息。

**1。用户注册完成**
**2。用户登录完成**

1.  刷新 JWT
2.  用户注销
3.  获取当前用户的信息
4.  产生一个想法
5.  删除一个想法
6.  获得一页的想法
7.  更新想法

当前日期和时间:**星期四 2019 年 3 月 8 日 02:13**
交货日期:**Fri 2019 年 3 月 8 日晚上 11:59**T5】当前花费时间:**0 HR 55 m11s**T8】总花费时间: **10hr44m19s**

* * *

刷新令牌:

```
POST /some-token-endpoint/refresh 
```

Enter fullscreen mode Exit fullscreen mode

```
{'jwt': '', 'refresh_token': 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NTIwMzA5OTcsIm5iZiI6MTU1MjAzMDk5NywianRpIjoiYjBjOGNhNDMtZmQzYi00OWI5LWFjM2YtZmU4ZWU0YjljY2UyIiwiZXhwIjoxNTUyMDMxNTk3LCJpZGVudGl0eSI6MSwidHlwZSI6InJlZnJlc2gifQ.9Akh0uY4ou8DBarkp2lDqS38dlIebqnwk0ss3J5B05s'} 
```

Enter fullscreen mode Exit fullscreen mode

```
JWT="eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NTIwMzA5OTcsIm5iZiI6MTU1MjAzMDk5NywianRpIjoiMWQ5NmU4MGQtOWQyZi00Mjg2LWEyMzItNGYxNDY5NjI5MGYyIiwiZXhwIjoxNTUyMDMxODk3LCJpZGVudGl0eSI6MSwiZnJlc2giOmZhbHNlLCJ0eXBlIjoiYWNjZXNzIn0.HyMCR5mFdcoUi7VUjA3LeiTDGn8Ccsy08Y9Bbc0LL9M"
REFRESH_TOKEN="eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NTIwMzA5OTcsIm5iZiI6MTU1MjAzMDk5NywianRpIjoiYjBjOGNhNDMtZmQzYi00OWI5LWFjM2YtZmU4ZWU0YjljY2UyIiwiZXhwIjoxNTUyMDMxNTk3LCJpZGVudGl0eSI6MSwidHlwZSI6InJlZnJlc2gifQ.9Akh0uY4ou8DBarkp2lDqS38dlIebqnwk0ss3J5B05s"
./curl_refresh.sh "$JWT" "$REFRESH_TOKEN" 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们正在成功地返回新的 jwt！！！

* * *

给自己的提示，以避免将来的麻烦:

```
JWT_HEADER_NAME 
JWT_HEADER_TYPE 
```

Enter fullscreen mode Exit fullscreen mode

flask-jwt-extended 中有很多配置选项

* * *

**1。用户注册完成**
**2。用户登录-完成**
**3。刷新 JWT**

1.  用户注销
2.  获取当前用户的信息
3.  产生一个想法
4.  删除一个想法
5.  获得一页的想法
6.  更新想法

当前日期和时间:**Fri 2019 年 3 月 9 日 02:51**
交货日期:**Fri 2019 年 3 月 8 日 11:59 PM**
当前花费时间: **0hr35m23s**
总花费时间: **11hr19m42s**

* * *

注销

*   [https://flask-jwt-extended . readthedocs . io/en/latest/black list _ and _ token _ revoking . html](https://flask-jwt-extended.readthedocs.io/en/latest/blacklist_and_token_revoking.html)

似乎我们做到了。

* * *

**1。用户注册完成**
**2。用户登录-完成**
**3。刷新 JWT**
**4。用户注销**

1.  获取当前用户的信息
2.  产生一个想法
3.  删除一个想法
4.  获得一页的想法
5.  更新想法

当前日期和时间:**Fri 2019 年 3 月 8 日 04:56**
交货日期:**Fri 2019 年 3 月 8 日 11:59PM**T5】当前花费时间: **0hr20m08s**
总花费时间: **11hr39m50s**

* * *

获取当前用户的信息
这非常简单。
我们仍然需要做一些关于 gravatar url 的事情。
我们可以回到这个话题。

**1。用户注册完成**
**2。用户登录-完成**
**3。刷新 JWT**
**4。用户注销**
**5。获取当前用户的信息**

1.  产生一个想法
2.  删除一个想法
3.  获得一页的想法
4.  更新想法

当前日期和时间:**Fri 2019 年 3 月 8 日 05:10**
交货日期:**Fri 2019 年 3 月 8 日 11:59 PM**
当前花费时间: **0hr10m44s**
总花费时间: **11hr50m34s**

* * *

产生一个想法

到目前为止一切顺利　‘...

**1。用户注册完成**
**2。用户登录-完成**
**3。刷新 JWT**
**4。用户注销**
**5。获取当前用户的信息**
**6。创造一个想法**

1.  删除一个想法
2.  获得一页的想法
3.  更新想法

当前日期和时间:**Fri 2019 年 3 月 8 日 06:08**
交货日期:**Fri 2019 年 3 月 8 日 11:59 PM**
当前花费时间: **0hr51m00s**
总花费时间: **12hr41m34s**

* * *

删除一个想法

*   验证意见 id 是否有效并存在
*   从数据库中删除该想法

废话...

如何在 Flask 中创建包含 id 的动态路由？？？

*   [https://stack overflow . com/questions/15117416/capture-arbitrary-path-in-flask-route](https://stackoverflow.com/questions/15117416/capture-arbitrary-path-in-flask-route)

看起来不错。

我他妈的饿了。

**1。用户注册完成**
**2。用户登录-完成**
**3。刷新 JWT**
**4。用户注销**
**5。获取当前用户的信息**
**6。创造一个想法**
**7。删除一个想法**

1.  获得一页的想法
2.  更新想法

当前日期和时间:**Fri 2019 年 3 月 8 日 06:25**
交货日期:**Fri 2019 年 3 月 8 日 11:59 PM**
当前花费时间: **0hr15m00s**
总花费时间: **12hr56m34s**

* * *

我们休息了几天，但是

# 获得一页创意

首先，登录，然后将 JWT 传递给/getideas

```
{'jwt': 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NTIyMzkzMDMsIm5iZiI6MTU1MjIzOTMwMywianRpIjoiYzc3NGIyZWUtZTVmZi00ZTUzLWI2ZjktYjAyNDkxZWI1ZDI4IiwiZXhwIjoxNTUyMjQwMjAzLCJpZGVudGl0eSI6MSwiZnJlc2giOmZhbHNlLCJ0eXBlIjoiYWNjZXNzIn0.NJDvj57c_hxqoqt5N_0zPYFHqxJOOjhMdAaQ_jIIcR4', 'refresh_token': 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NTIyMzkzMDMsIm5iZiI6MTU1MjIzOTMwMywianRpIjoiNzc5ZDYzZTctZjM0Ni00MjQ1LWFlMTEtODkwMDM0OGQ3OWYyIiwiZXhwIjoxNTUyMjM5OTAzLCJpZGVudGl0eSI6MSwidHlwZSI6InJlZnJlc2gifQ.xX9a60sgVZ01OHjFwSPFF8EV-E_oS1YBfLiglGECYL4'} 
```

Enter fullscreen mode Exit fullscreen mode

我们正试图让我们的想法回到 json。

```
File "/usr/local/Cellar/python/3.7.2_2/Frameworks/Python.framework/Versions/3.7/lib/python3.7/json/encoder.py", line 179, in default
    raise TypeError(f'Object of type {o.__class__.__name__} '
TypeError: Object of type Idea is not JSON serializable 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们如何使 Python JSON 中的对象可序列化呢？

*   [https://stack overflow . com/questions/21411497/flask-JSON ify-a-list-of-objects](https://stackoverflow.com/questions/21411497/flask-jsonify-a-list-of-objects)

我找到的一个变通办法是打电话。serialize()一旦被定义。

```
retval = jsonify([idea.serialize() for idea in idea_page.items]) 
```

Enter fullscreen mode Exit fullscreen mode

我想这就完成了！

**1。用户注册完成**
**2。用户登录-完成**
**3。刷新 JWT**
**4。用户注销**
**5。获取当前用户的信息**
**6。创造一个想法**
**7。删除一个想法**
**8。获得一页创意**

1.  更新想法

当前日期和时间:**太阳 2019 年 3 月 10 日 13:54**
交货日期:**Fri 2019 年 3 月 8 日 11:59PM**T5】当前花费时间:**0 HR 20 m00 s**T8】总花费时间: **13hr16m34s**

* * *

# 更新一个想法

搞定了。！！

当前日期和时间:**太阳 2019 年 3 月 10 日 14:53**
交货日期:**Fri 2019 年 3 月 8 日 11:59PM**T5】当前花费时间:**0 HR 17m 30s**T8】总花费时间: **13hr34m04s**

* * *

好了，好像就这些了。

我们确实忽略了 gravatar url。

我们现在没有足够的时间提交，但我们基本上完成了。

# 你需要做什么:

1.  为您的最终代码提交创建一个私有的 Github 或 Bitbucket 存储库
2.  将我们的 Github 或 Bitbucket 帐户添加到您的存储库中
3.  将您的代码部署到在线版本
4.  向我们发送电子邮件，告知以下信息:
    *   电子邮件主题:
    *   小型后端项目:xxxxx
    *   你的名字
    *   您的存储库链接
    *   一个 API 服务器链接，我们可以在那里访问和测试

* * *

# URL 挖掘器

```
https://en.gravatar.com/site/implemeimg/ 
```

Enter fullscreen mode Exit fullscreen mode

1.  计算电子邮件哈希
2.  https://www.gravatar.com/avatar/HASH

```
https://en.gravatar.com/site/implement/hash/ 
```

Enter fullscreen mode Exit fullscreen mode

```
echo md5( strtolower( trim( "MyEmailAddress@example.com " ) ) ); 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它转换成 Python。

```
python3

import hashlib
email = 'youremail@example.com'
a = hashlib.md5(email.lower().encode())
a.hexdigest() 
```

Enter fullscreen mode Exit fullscreen mode

得到我们想要的。

```
gravURL = 'https://www.gravatar.com/avatar/' + str(a.hexdigest()) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要这发生在用户注册。

这是在`routes.py`里。

为此，我们需要更新`models.py`文件中的`User`定义。

或者，我们可以每次都计算返回吗？

也许我们可以避免更新模型。

注意:在强制转换之前检查类型。写代码的时候真的要用脑子。我知道我们需要完成任务。

我通过在`getCurrentUserInfo`中设置字段来预测这一点。

问题解决了。

提交时间。

* * *

1.  创建私有 github

旁注:绿茶时间

完成的

* * *

1.  将我们的代码部署到在线版本

*   [http://flask.pocoo.org/docs/1.0/deploying/](http://flask.pocoo.org/docs/1.0/deploying/)

我们将尝试`localtunnel`方法，这似乎是最少的麻烦。今晚我不想学什么英雄学什么的。

现代本地隧道:

*   [https://localtunnel.github.io/www/](https://localtunnel.github.io/www/)

现在命令是`lt`。

废话...刚刚意识到我将不得不安装许多依赖项`-_-;...`

```
sudo apt install python3-flask -y 
python3-flask-sqlalchemy
python3-flask-migrate
python3-flask-jwt-extended

sudo apt install python3-pip
pip install setuptools
pip install flask-jwt-extended 
```

Enter fullscreen mode Exit fullscreen mode

好的，在我的服务器上运行了。

看来我们不得不这样做:

*   [http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/](http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/)

* * *

我很累了。我们将在早上在一台全新的服务器上完成这项工作。

当前日期和时间:**星期三 2019 年 3 月 13 日 4:39**
交货日期:**Fri 2019 年 3 月 8 日 11:59PM**T5】当前花费时间: **1hr40m41s**
总花费时间: **15hr14m04s**

* * *

晚上回来看，哈哈。ffff

启动一个服务器来托管这个东西。

走阿帕奇 mod_wsgi 路线。

首先要保护服务器。

完成了。

接下来，安装依赖项。

```
sudo apt-get install python3-pip \
python3-flask \
python3-flask-sqlalchemy \
python3-flask-migrate -y

pip3 install setuptools
pip3 install flask-jwt-extended 
```

Enter fullscreen mode Exit fullscreen mode

完成了。

现在，剩下的就是将应用程序代码移动到适当的目录中，并开始 wsgi 设置。

当前日期和时间:**2019 年 3 月 13 日 22:31**
交货日期:**Fri 2019 年 3 月 8 日 11:59PM**T5】当前花费时间: **0hr20m00s**
总花费时间: **15hr34m04s**

* * *

这太难了...我已经完成了所有的步骤，但是当我试着端上来的时候，总是遇到 500 个错误。我认输了。
等等...
上菜了！！！
我们做到了！！！

*   [https://www . bogotobogo . com/Python/Flask/Python _ Flask _ hello world _ App _ with _ Apache _ WSGI _ Ubuntu 14 . PHP](https://www.bogotobogo.com/python/Flask/Python_Flask_HelloWorld_App_with_Apache_WSGI_Ubuntu14.php)
*   [https://stack overflow . com/questions/19344252/how-to-install-configure-mod-wsgi-for-py3](https://stackoverflow.com/questions/19344252/how-to-install-configure-mod-wsgi-for-py3)
*   [https://www . Li node . com/docs/websites/hosting-a-website-Ubuntu-18-04/](https://www.linode.com/docs/websites/hosting-a-website-ubuntu-18-04/)
*   [https://modwsgi.readthedocs.io/en/develop/installation.html](https://modwsgi.readthedocs.io/en/develop/installation.html)
*   [http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/](http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/)

* * *

终于提交了一切

最终时间日志！！！

当前日期和时间:**星期四 2019 年 3 月 14 日 2:59**
交货日期:**Fri 2019 年 3 月 8 日晚上 11:59**T5】当前花费时间:**1 HR 02 m00 s**T8】总花费时间: **16hr36m04s**

在大约 2-3 周的时间里，我设法挤出了 16 个小时的额外“时间”,以一种真正速成的方式学习“烧瓶”框架。

然而。

我现在能够使用 JWT 令牌登录，通过 Flask 和 mod_wsgi 开发和部署 web 应用程序。

格式化这个博客材料有很多事情要做，因为它真的很混乱/漫无目的，但我想很多人会发现这很有用，很有趣。

* * *

**此博客中的所有网址**

*   [https://jwt.io/introduction/](https://jwt.io/introduction/)
*   [https://tools.ietf.org/html/rfc7519](https://tools.ietf.org/html/rfc7519)
*   [https://blog . miguelgrinberg . com/post/the-flask-mega-tutorial-part-ii-templates](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-ii-templates)
*   [https://blog . miguelgrinberg . com/post/the-flask-mega-tutorial-part-iv-database](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-iv-database)
*   [https://pythonhosted.org/Flask-JWT/](https://pythonhosted.org/Flask-JWT/)
*   [https://flask-jwt-extended . readthedocs . io/en/latest/installation . html](https://flask-jwt-extended.readthedocs.io/en/latest/installation.html)
*   [https://real python . com/token-based-authentic ation-with-flask/](https://realpython.com/token-based-authentication-with-flask/)
*   [https://flask-jwt-extended . readthedocs . io/en/latest/black list _ and _ token _ revoking . html](https://flask-jwt-extended.readthedocs.io/en/latest/blacklist_and_token_revoking.html)
*   [https://stack overflow . com/questions/15117416/capture-arbitrary-path-in-flask-route](https://stackoverflow.com/questions/15117416/capture-arbitrary-path-in-flask-route)
*   [https://stack overflow . com/questions/21411497/flask-JSON ify-a-list-of-objects](https://stackoverflow.com/questions/21411497/flask-jsonify-a-list-of-objects)
*   [http://flask.pocoo.org/docs/1.0/deploying/](http://flask.pocoo.org/docs/1.0/deploying/)
*   [https://localtunnel.github.io/www/](https://localtunnel.github.io/www/)
*   [http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/](http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/)
*   [https://www . bogotobogo . com/Python/Flask/Python _ Flask _ hello world _ App _ with _ Apache _ WSGI _ Ubuntu 14 . PHP](https://www.bogotobogo.com/python/Flask/Python_Flask_HelloWorld_App_with_Apache_WSGI_Ubuntu14.php)
*   [https://stack overflow . com/questions/19344252/how-to-install-configure-mod-wsgi-for-py3](https://stackoverflow.com/questions/19344252/how-to-install-configure-mod-wsgi-for-py3)
*   [https://www . Li node . com/docs/websites/hosting-a-website-Ubuntu-18-04/](https://www.linode.com/docs/websites/hosting-a-website-ubuntu-18-04/)
*   [https://modwsgi.readthedocs.io/en/develop/installation.html](https://modwsgi.readthedocs.io/en/develop/installation.html)
*   [http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/](http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/)

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)