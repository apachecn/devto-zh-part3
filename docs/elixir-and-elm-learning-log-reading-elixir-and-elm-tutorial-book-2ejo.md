# 仙丹和榆树学习日志。阅读《长生不老药和榆树教程》一书。

> 原文：<https://dev.to/antonrich/elixir-and-elm-learning-log-reading-elixir-and-elm-tutorial-book-2ejo>

我想，去年 11 月，我在浏览 leanpub store，发现了一本叫[《仙丹与榆树教程》](https://leanpub.com/elixir-elm-tutorial)的小书。我立刻就被吸引住了。因为我对这两种语言都感兴趣，而且我听说 Elixir 和 Elm 的组合非常好。

如果你像我一样资金紧张，这本书在价格上很容易买到。

我已经读完了这本书的一半，但是我想把我正在阅读的日志发表出来。

日志本身是零星的，混乱的。我记录了我的错误和印象。

我也有一个巨大的惊喜。

作者是一个思想非常开放的人，我现在正在和他交流。大声向 [Bijan](https://leanpub.com/u/bijanboustani) 致敬。

还有一件事，我在 Ubuntu 上，所以我使用的一些命令可能无法在你的发行版或系统上运行。几周前，我安装了 Antergos(基于 arch 的发行版)，我又从头开始了，所以命令会在另一篇文章中改变。

开始了。以下是日志:

2018 年 11 月 19 日。

从晚上开始。

我已经在学榆树和仙丹了。榆树多于仙丹。在这一点上，我学到的 Haskell 比 Elixir 还多。

我熟悉函数式编程。但我正在学习，这意味着我还没有在这个行业工作过，我还没有找到工作并经历面试过程。因此，这本书是我学习长生不老药，然后创建几个组合项目的途径。

我已经在我的机器上将 Elm 更新到 0.19，而这本书的版本是 0.18。

因为我也有一个更新的药剂(1.7.3)。

我买书前的第一个问题是“书里用到的数据库是什么”。在 leanpub 上的表格目录中或者在书的描述中说一些关于 postrgeSQL 的事情是很好的。

我知道 heroku 就像一个数字海洋。
个人项目免费吗？这是怎么回事？

* * *

mix phx.new 平台

给出一个错误:

找不到任务“phx.new”

大概是我没装凤凰吧。

我已经用
mix archive . install hex phx _ new 1 . 4 . 0 安装了

我是否安装了 ecto？

它是安装的(可能是和 phoenix 一起安装的)。

* * *

我第一次尝试通过输入命令
psql postgres 将密码改为 postgreSQL(它没有要求我输入密码)

然后我设置了一个新的密码。但是没有成功，然后我运行了一个在 stackoverflow 上找到的命令，它成功了。

[https://stack overflow . com/questions/35785892/ecto-postgres-install-error-password-authentic ation-failed](https://stackoverflow.com/questions/35785892/ecto-postgres-install-error-password-authentication-failed)

sudo -u postgres psql -c "更改用户 postgres 密码' postgres '；"

sudo 服务 postgresql 重新启动

* * *

所以这个问题已经解决了，我有一个凤凰的应用程序。

*mix phx.server*

然后

本地主机:4000

雅虎，一切正常。

* * *

混合 phx.gen.html 帐户玩家玩家用户名:字符串分数:整数

我在读关于路由的书。我还不知道路由是什么。

* * *

我开始使用 html 模板，遇到了类的问题。

```
<div class="container">
    <a class="btn btn-success" href="/players/new">Create Player Account</a>
    <a class="btn btn-info" href="/players">List All Players</a>
</div> 
```

我已经通读了 phoenix 1.4 的发行说明。自举被放弃，取而代之的是毫克[https://milligram.io/](https://milligram.io/)

哦，毫克版看起来没有 bootstrap 版牛逼。但是这里没有评判，因为在根据你的需要调整网站的外观时，可能会有更少的摩擦。据我所知，凤凰城的人更了解。

我埋头读完了这一章。

这本书以那一章开始很酷。

还有我不知道的 IO.inspect。并且有很好的局部应用例子。

* * *

我搞砸了我的博士考试。我记得必须有非常具体的缩进。

我安排的。

* * *

已经了解管道操作员。快速浏览一遍。

* * *

关于 Elixir 中的 arity，有趣的是，您可以用相同的名称但不同的 arity 来定义函数。看到例子会很有趣，但我也觉得会带来困惑。

* * *

模式匹配。

这就是 Haskell 知识的用武之地。虽然我在接触 Haskell 之前已经在 Elixir 中看过模式匹配。Haskell 让它变得如此简单。

警卫很有趣。
速记功能语法。
is_(点击 iex 中的选项卡以查找不同的功能)

好了，我要完成长生不老药的介绍了。

* * *

我想我需要休息一下。

凤凰测试和部署。

我想我需要更好地使用 git。我只是浏览了一些简单的教程。

我刚刚有了一个想法，为人们创建一个虚拟存储库来进行拉请求，只是为了训练他们的 git 技能。

顺便说一下，我觉得这本书的结构很有趣。

Git ->所以，我去了 dev.to 找到了一个简单的 Git 教程，快速浏览了一下教程。我必须注意到，我经常在程序员中看到像“困难”和“困难”这样的事情(这经常会引起误解)。还是我，一个有技术思维的人，似乎并不为这些“困难”所困扰？

[https://dev.to/juni/git-and-github-必须知道的命令-做出你的第一次承诺-333c](https://dev.to/juni/git-and-github---must-know-commands-to-make-your-first-commit-333c)

来自作者[https://git-scm.com/](https://git-scm.com/)的链接

这是我之前那个问题的答案:

对于那些以前没有使用过 Heroku 的人来说，它本质上为我们提供了一种简单而免费的方法来部署我们的应用程序并查看它的运行情况。

刚在 Heroku 签约。让我们看看接下来会发生什么。

仙丹平台
将成为 heroku 上应用的名字。
我还不知道这些名字是否会引起问题，因为它们与 github 的名字不同。

sudo snap install -安装了 heroku 的经典 Heroku 7 . 18 . 9 版

转到 heroku:远程和 YOURAPPNAME

这个很混乱，因为它应该是 YOURAPPNAME_ON_HEROKU 或者 YOURAPPNAME_ON_GITHUB

应该是:HEROKU git:remote-a your app name _ ON _ HEROKU

使用浏览器登录 heroku
**。这是一种很酷的登录方式。**

对于 elixir_buildpack.config
，我需要使用
Erlang _ version = 21.0
elixir _ version = 1 . 7 . 3

mix phx . gen . secret
5k 7 hfvl+mpqhxsgg 7 JF f1 Xi 3 icwv/XG/qsgoxislwyvk qcfl 4 wwrhbapbrwue

在调整 prod.exs 之前，我制作了一个备份版本 prod_backup.exs。

因为它看起来和书上的不一样，可能有一些变化。

我在赫罗库的部署不成功。
将会阅读这个[https://hexdocs.pm/phoenix/heroku.html](https://hexdocs.pm/phoenix/heroku.html)，但首先我会谷歌一下错误信息

错误:无法将一些参考推送到'[https://git.heroku.com/platform-for-elixir.git[T1 ' '](https://git.heroku.com/platform-for-elixir.git)

决定更仔细地检查 Heroku 的日志。

什么没用:
git push -f heroku master
更改 proc file
phoenix _ static _ build pack . config
always _ rebuild = true

我发现我可以用

[https://gigalixir.readthedocs.io/en/latest/](https://gigalixir.readthedocs.io/en/latest/)

1 个实例+ 1 个数据库可以免费使用。

https://github.com/bitwalker/distillery 也是一个可能的解决方案。

[https://alchemist.camp/episodes/deploy-phoenix-heroku](https://alchemist.camp/episodes/deploy-phoenix-heroku)

找到了这些，值得一看:

[https://github . com/dwyl/learn-heroku/blob/master/elixir-phoenix-app-deployment . MD](https://github.com/dwyl/learn-heroku/blob/master/elixir-phoenix-app-deployment.md)
[https://github.com/dwyl/phoenix-chat-example](https://github.com/dwyl/phoenix-chat-example)

* * *

11 月 20 日 14:15

昨天，我花了整整一个晚上的时间试图将应用程序部署到 heroku。没有成功。我会继续看这本书。

这是事情变得不同的另一个地方:

```
Player Changesets

def changeset(player, attrs) do
    player
    |> cast(attrs, [:username, :score])
  end 
```

* * *

11 月 20 日 22:37

我决定从头开始。

* * *

我已经设置了 emmet 来处理 html.eex

现在，我需要注意埃米特没有垂直展开线条。

再次尝试部署:

尝试了这个:

第 1 行:早午餐:命令未找到
[https://github . com/gjaldon/heroku-build pack-phoenix-static/issues/24](https://github.com/gjaldon/heroku-buildpack-phoenix-static/issues/24)

没起作用

* * *

十一月二十一日。17:39

在我与作者协调后，他将我链接到他的书中的更新。

现在我已经将应用程序部署到 heroku。

是时候继续了。我会尝试阅读更新版本。

阅读更新凤凰注册章节。

当我运行迁移[https://github . com/elixir-elm-tutorial/elixir-elm-tutorial-book/blob/revisions/subscript/phoenix _ sign _ up . MD # running-the-migration](https://github.com/elixir-elm-tutorial/elixir-elm-tutorial-book/blob/revisions/manuscript/phoenix_sign_up.md#running-the-migration)
时，我遇到了错误:
** (RuntimeError)找不到#PID < 0.91.0 >的迁移运行程序进程

解决方法:
[【https://github.com/elixir-ecto/ecto/issues/1298】](https://github.com/elixir-ecto/ecto/issues/1298)
把[【https://github.com/elixir-ecto/ecto/issues/1298】](https://github.com/elixir-ecto/ecto/issues/1298)放在零钱函数里面。

啊，又一次，我蠢到没有仔细看代码。应该是在 change 函数里面！

我完成了更新凤凰注册。

我刚刚注意到，在我第一次尝试之前，我实际上跳过了这一章！我需要更加注意。

* * *

凤凰认证

这句话有点棘手

让我们继续用下面的代码更新我们的 create_player/1 测试用例，因为我们希望用户使用有效的用户名和密码字段创建帐户。

这本书大量引用了文献资料。这是正确的事情。因为你真的不需要重复你自己，也不需要关注官方文档中没有的东西。

顺便说一下，Elixir 的文档很棒。

阅读关键字列表和地图。

这里有一张地图:
%{:a = > 1，2 = > :b}

但这也是一个地图
%平台。accounts . Player { password:" some password "，username: "some username"}

语法之间到底有什么区别？

答案如下:
当 map 中的所有键都是原子时，为了方便起见，可以使用关键字语法:

现在，在阅读了关于长生不老药的文献后，这变得更有意义了

我们将做一点小小的改动，删除密码字段。我们需要将该结构转换成一个 map 来删除该字段，然后我们将这些字段合并在一起以返回 player 结构。

```
def player_fixture(attrs \\ %{}) do
  {:ok, player} =
    attrs
    |> Enum.into(@valid_attrs)
    |> Accounts.create_player()

  player_attrs_map =
    player
    |> Map.from_struct()
    |> Map.delete(:password)

  Map.merge(%Player{}, player_attrs_map)
end 
```

唯一一件看起来奇怪而且我不明白的事情是这个
def player_fixture 中的
attrs \ % { }(attrs \ % { })做的

认证功能

我得到一个错误:
** (KeyError) key:在:%{current_player: nil}中找不到 current_user

默认情况下，我的页面控制器如下所示:

def index(conn，_params) do
render(conn，" index.html")
end

应该是:

def index(conn，_params) do
render conn，" index.html"
end

我在想为什么没有括号？仍然没有，这似乎不是问题。

key:在:%{current_player: nil}
中找不到 current_user，localhost:4000
指向

lib/platform _ web/controllers/page _ controller . ex

```
 def index(conn, _params) do

    render conn, "index.html"

  end

  defp authenticate(conn, _opts) do

    if conn.assigns.current_user() do

      conn

    else

      conn

      |> put_flash(:error, "You must be signed in to access that page.")

      |> redirect(to: Routes.player_path(conn, :new)) 
```

11 月 22 日 17:13
现在我决定重新键入一些代码。现在，我要从头再来一遍。

昨天，我有了一个想法，如果我阅读 phoenix 文档并尝试获得对该框架的一般理解会怎么样。我现在就去做。

随便看了一点概览页
[https://hexdocs.pm/phoenix/overview.html](https://hexdocs.pm/phoenix/overview.html)

* * *

11 月 23 日 17:57

我又到了凤凰认证章。

认证插头

如果我发现了一个错误并且无法修复它。我将只是复制代码，并尝试看看错误是否得到修复。如果没有，我会给作者发电子邮件。

您必须登录才能访问该页面

是的，很好。现在我没有以前的毛病了。

正在登录
PlayerAuthController。

会话路由
[https://github . com/elixir-elm-tutorial/elixir-elm-tutorial-book/blob/revisions/subscripts/phoenix _ authentic ation . MD](https://github.com/elixir-elm-tutorial/elixir-elm-tutorial-book/blob/revisions/manuscript/phoenix_authentication.md)
我因为某种原因出现了一个可怕的 bug，却没有任何特别的见解为什么。

所以我决定检查我的代码两次，发现我有一个区别

get "/"，PlayerController，:new
resources "/players "，player controller
resources "/sessions "，PlayerSessionController，only: [:new，:create，:delete]

我让
获取“/”，PlayerController，:index

我把它改成了:new
，错误信息更加可怕。但是我又把它改成了:index，错误就消失了。

11 月 24 日 00:16 刚写完凤凰认证章。

* * *

在书中，有一个用-S mix
iex -S mix phx.server 启动 iex 的推荐

我试着在没有 phx.server 的情况下做这件事，但它成功了。有了它，结果是一样的，只是你有一个 phx 服务器在运行。

def index(conn，_ params)do
conn 参数看起来像某种约定。

@conn
是什么意思？

Accounts.get_player！(id)
为什么会有感叹号？

Phoenix Api 和 JSON 章节

```
def render("index.json", %{players: players}) do
    %{data: render_many(players, PlayerApiView, "player.json")}
  end

  def render("show.json", %{player: player}) do
    %{data: render_one(player, PlayerApiView, "player.json")}
  end 
```

为什么 render 同名？这难道不矛盾吗？还是只是模式匹配？

11 月 24 日 22:24 刚看完 API 章节。

* * *

老实说，到目前为止，我真的不知道我在做什么。似乎我应该更深入一点，试着去理解。

似乎作者真的认为读者是熟悉 Ruby on Rails 的 Ruby 爱好者。我没有使用过这样的框架，所以我不知道它是什么。

必须深入学习。

* * *

第一章:榆树简介

然后，我们还将使用 Elm 开始为我们的平台构建迷你游戏。

构建游戏是令人兴奋的。

我等不及了。

现在，我不打算深入介绍，我只是快速浏览这一章。

main:Html msg
main =
“Hello World”
|>string . toupper
|>text

这是一些不错的榆树代码。

就是这样。我不干了。到下一章。

* * *

ELM 设置。

我刚刚注意到这本书有一个很好的大纲。

11 月 25 日 02:07 刚写完章。

* * *

Elm 应用

让我们更新我们的主函数来接受来自我们模型的数据，我们的应用程序应该回到一个完全运行的状态。我们将在这里发布完整的代码示例，然后根据数据更好地理解它是如何流经我们的函数以及它们是如何组合在一起的(还要注意，我们删除了 firstGameMaybe 和 firstGameTitle 函数，因为我们正在映射所有的标题):

我看不出模型是如何出现在 gameListItem 函数中的。

好的，我明白了。它在主函数中。调用 gamesIndex 函数时将模型作为参数。

现在我清楚地看到什么是什么。首先演示了调用列表中的第一项时的可能类型，然后是整个列表的映射。

现在我有一个问题。这段代码可以用管道重构吗？

11 月 25 日 23:30 整理榆树申请章节。

* * *

榆树建筑

我看到了订阅，所以我立即设定了期望，希望看到更多关于 Cmd 的内容。这正是我缺乏理解的地方。

期待那部分。

11 月 27 日 00:49 结束

一直在学一些 Haskell。在 edx.org 慢慢完成 FP 课程。

* * *

现在转到 ELM api 数据章节。

Decode.field " description " Decode.string
如果我们无论如何都要使用 decode . string，为什么还要使用 decode . field？

02:38 我厌倦了电脑，开始在 twitter 上浪费时间。关掉电脑，躺下来享受我的存在。

11 月 27 日继续 elm api 数据章节。
17:13

好的，我对 Http 有一些问题。
该不该公开 Http？
导入 Http 暴露(..)

我找不到一个`Http.send`变量:

62 | | > http . send fetchgamelist
^^^^^^^^^
`Http`模块不公开`send`变量。不过这些名字看起来很接近
:

我就这个问题给作者发了电子邮件。我试着自己解决。我不能。结果是 Http.get 被更改了。因此，我将通过榆树的官方指南。谁知道也许我会自己找到解决办法。

* * *