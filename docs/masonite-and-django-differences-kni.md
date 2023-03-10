# Masonite 和 Django 的差异

> 原文：<https://dev.to/masonite/masonite-and-django-differences-kni>

# 泥瓦匠和姜戈

我总是被问到“Masonite 和 Django 相比如何？”或者“为什么我会选择 Masonite 而不是 Django？”在这篇文章中，我将尝试解决 Masonite 的许多方面，以及它们与 Django 的比较。这篇文章很可能是一个正在进行的部分增加的过程。

## 免责声明

在我开始之前，我不是说姜戈很糟糕。Django 是一个很棒的框架，它已经在 Python 社区中发展很长时间了。很明显，这种情况会持续下去。我个人喜欢其他框架的一些特性，也喜欢用 Python 编写，所以我从其他框架中获取了工具、技巧和架构概念，并创建了一些完全符合我个人用例的东西。

Masonite 做事情的方式与众不同，我将是第一个承认它在我和我们的维护人员编码和我们的开发人员实践中有点固执己见的人。

如果您试用了 Masonite 并意识到它是一个很棒的框架，那么我希望您继续使用它并为社区做出贡献。如果你不喜欢 Masonite，而是认为 Django 比你更有力量；我希望你继续使用 Django 并创造一些令人敬畏的软件，尽管我们会想念你。

在每一节中，我会比 Django 更多地谈论 Masonite，因为我假设你已经理解了 Django，所以这并不是说我故意有偏见或只解释其中一个方面。如果我提到“设置”之类的东西，我会假设你理解 Django 设置是如何工作的。

我将稍微解释一下 Masonite 幕后的东西，但是如果你想更深入地了解它并学习它实际上是如何工作的，可以添加到文档的链接。

## 思想

Masonite 完全以开发者为中心。我们试图得到一个考虑到开发人员的框架，以及开发人员每天将如何与框架交互，并努力以一种有意义的方式调整交互。我们集中了一个完整的版本，致力于完善框架中的细节。Masonite 不是为新闻集团(Django)编写的框架，也不是作为愚人节玩笑(Flask)编写的框架。虽然这两个框架已经奋斗了很长时间，并且仍然是两个最占优势的 Python 框架，但 Masonite 将是一个非常适合的框架，并且是由开发人员为开发人员编写的框架。

Masonite 是为那些周末战士项目，可以成为一些东西。这是为了下一个 SaaS 的想法，你想写，但害怕写所有的锅炉板涉及获得它的生产或上传到 S3 或与 Stripe 集成或获得一个 API 设置让开发人员连接到您的服务或获得循环任务设置与 cron 或所有那些烦恼，只需要几分钟设置，但你需要设置 10 个不同的包只是为了到达你想开始开发的地方。

您知道您将需要 whitenoise 来处理静态文件，您知道您将需要接受 Stripe 订阅，您知道您将需要一些 websocket 请求来使您的应用程序响应更快。当您可以使用一个已经为您准备好的框架时，为什么还要担心这些设置呢？Masonite 是你下一个想法的绝佳启动项目，已经打包好，等待处理你的应用逻辑。

# 建筑

Masonite 和 Django 之间的架构完全不同。不仅 Masonite 遵循 MVC 架构，Django 也遵循 MTV 架构。

不，这些不一样。如果它们是一样的，那么它们会被称为一样的东西。是的，他们都完成完全相同的事情，但他们这样做的方式不同。

## 姜戈

Django 旨在将单个应用程序分解成多个“应用程序”。这是一个很好的架构，可以为那些需要它的开发人员抽象出许多逻辑，但许多开发人员不知道他们的应用程序的哪些部分需要在单独的应用程序中，所以他们开始将它们分开，直到他们意识到这只是增加了复杂性，因为现在我必须知道我将这个特定的模型放在哪里才能玩得好。

Django 没有真正的 MVC 架构。他们的架构，就像他们在 MTV 中所说的那样，是模型模板视图。

## 石匠

Masonite 非常不同。Masonite 被设计成一个像 Django 一样的应用程序，但是它把逻辑分解成多个控制器，这些控制器在它们自己的模块中。

与许多其他 Python 框架不同，Masonite 具有真正的 MVC 架构。Masonite 拥有控制器类和方法，可以用来以一种简洁实用的方式真正抽象出大量的业务逻辑。

# 服务容器和服务提供者

## 姜戈

Django 版本的 Masonite 的服务容器是他们的 INSTALLED_APPS。一些第三方应用程序需要在 INSTALLED_APPS 列表中添加一个应用程序，将功能加载到框架中。Django 使用字符串来加载这些应用程序，并在应用程序中添加功能。一些第三方软件包不需要这样做。

## 石匠

Masonite 很早就采用了服务容器的概念。服务容器只是一种简单地向 Masonite 添加功能的方式，mason ite 在整个应用程序中都可用。这是 IOC 容器的一种形式，对象可以放入其中，也可以从中检索。Masonite 更进一步，允许容器“解析”对象。简单地说，它将根据参数确定您的对象需要什么，并从容器中检索这些对象。

* * *

点击阅读更多关于[服务容器的信息:](https://docs.masoniteproject.com/architectural-concepts/service-container)

* * *

这些特性以服务提供者的形式添加到 Masonite 中(因为它们提供了一种服务)。

服务提供商可以添加功能，如新的驱动程序、新的特性、完整的软件包、新的命令等等。例如，添加邮件服务提供商可以添加对发送电子邮件等的支持。

这些服务提供者(从 2.0 开始)是加快服务请求时间的类，因为它们不需要在后端被“找到”。如果你来自 Django，你可以把服务提供商看作是“向我的应用添加第三方应用”的一种形式虽然应用程序和服务提供商在幕后的表现非常不同，但非常高层次的概念是相似的。

* * *

# 驱动程序和设置

## 姜戈

Django 使用一些设置文件，并根据它们应该运行的位置对它们进行抽象。例如 se 可能有一个 settings.py 和一个 settings_local.py，这很有用，但是我个人不喜欢把我所有的设置都添加到一个配置文件中。对我来说这看起来有点乱，但是许多开发者喜欢它，这很好。我更喜欢另一种特性级别的抽象。这也可以在 Django 中通过将单个文件的所有设置导入到主设置文件中来完成，如果你选择这样做的话。

Django 不一定像 Masonite 一样有“驱动程序”的概念，但是添加这些对 Django 来说相当简单。它们通常需要 pip 安装和一些配置选项。

## 石匠

Masonite 的设置文件被分解成不同的开箱即用的特性，类似于:

```
config/
  application.py
  mail.py
  database.py
  ... 
```

您可以更改设置，并允许第三方应用程序为您创建设置。
Masonite 也有驱动程序的概念。因为 Masonite 的架构，东西可以随意打开、关闭和切换。如果你需要一个 postgres 数据库连接，那么你可以从“mysql”切换到“Postgres ”,这将切换应用程序的内部工作来使用 Postgres 连接。一切将保持不变，但使用另一个连接。

Masonite 也有几个现成的驱动程序，如 SMTP 驱动程序和用于发送电子邮件的 Mailgun 驱动程序。Masonite 将驱动程序用于多种用途，例如文件上传。我们可以简单地切换驱动程序，比如:

```
def show(self, Upload):
    Upload.driver('disk').store(..) # stores in the file system
    Upload.driver('s3').store(..) # stores into Amazon S3 
```

您还可以在配置文件中设置默认驱动程序，这样就不必在代码中显式声明它们。

由于 Masonite 契约的概念，我们可以确保相同类型的所有类都具有相同的方法，因此我们可以随时切换功能，一切都会正常工作。

如果你开始一个项目，把所有东西都上传到一个磁盘上，然后你的一个开发人员说我们真的应该转移到云上，那也没什么。要么使用 Masonite 提供的驱动程序，要么创建自己的驱动程序。

# 脚手架

这一部分有点固执己见，可能是也可能不是可以接受的做法，但它只是 Masonite 的一个选项，您可以完全跳过这一步。

“脚手架”的概念基本上意味着直接向代码添加锅炉板的能力。做一个模型或者控制器是极其重复的。

1.  创建文件
2.  创建类
3.  向它添加第一个方法

我们在应用程序中这样做了 100 次。这根本不需要花很长时间，但是对手头的任务来说，这是一种轻微的分心。它只减少了 15-30 秒的开发时间，但是如果你做 100 次，那么这可能会减少相当多的时间。

## 姜戈

Django 没有类级脚手架的概念。Django 有办法搭建一个新的应用程序，但没有能力搭建一个新的模型、视图或模板。

Masonite 将此向前推进了一步

## 石匠

Masonite 增加了创建模型、控制器和模板的能力(Masonite 称之为视图以保持 MVC 架构)。

我们有两个选择:

1.  找到控制器目录
2.  在控制器目录中创建文件
3.  在控制器文件中创建 DashboardController 类
4.  向它添加第一个方法

或者我们可以直接运行:

```
$ craft controller Dashboard 
```

这将创建文件、类、第一个方法(显然可以重命名)并把它放在正确的目录中。只是框架里的小东西让它感觉真的很牛逼。我一直在使用这些技能指令。

记住你不需要使用手工命令。这些通常只是助手命令，允许您在当天节省几秒到几分钟的开发时间。

# 包含电池

Django 和 Masonite 在功能方面有非常相似的电池概念，老实说，他们在开箱即用方面相当平等，但 Django 只需要对每个添加的功能进行一点点调整。

在这个舞台上，Masonite 和 Django 的区别不在于特性的数量，而在于 Masonite 开发人员如何与应用程序中的对象进行交互。Masonite 和 Django 之间的功能非常相似，我不会浪费时间解释 Django 的每个功能以及它们与 Masonite 的比较，但是您可以阅读 Masonite 文档以了解您正在寻找的任何功能信息。

任何框架都有特性，但真正重要的是我们作为开发人员如何与这些特性交互。我们用来与这些特性交互的方法和类决定了一个框架的成败。

Masonite 真正的亮点在于它是如何构建的，以及应用程序和控制器如何与 IOC 容器交互。仅这一点就应该真正推动许多开发人员使用 Masonite，但这与我们通常的开发方式有些不同。

一旦你开始与 Masonite 互动，你可以进一步了解它背后的架构。

* * *

## 你可以在从[请求生命周期](https://docs.masoniteproject.com/architectural-concepts/request-lifecycle)开始的文档中更深入地阅读架构

# 小事

Masonite 在开发时非常关注小事情，并且非常好地保持了“关注点分离”的方法。关注点的分离意味着代码单元应该以一种方式被分解，在这种方式下，它们只执行一项工作或者类似类别的工作。

## 抽象类方法

以 Django 为例:

```
def upload_file(request):
    if request.method == 'POST':
        # Some Logic Here To Upload
        return HttpResponseRedirect('/success/url/')
    return render(request, 'success.html', {'key': 'value'}) 
```

这是 Django 代码，它在 GET 请求中显示表单，在 POST 请求中存储表单。在 Masonite 中，我们将其进一步分解为`UploadController` :

```
class UploadController:

    def show(self):
        return view('upload', {'key': 'value'})

    def store(self):
        # Form Logic Here
        return view('success') 
```

根据我们的路线，upload/的 url 将指向 show 或 store，这取决于它是 GET 还是 POST(这些方法名称可以是您想要的任何名称)

所以逻辑的分离是 Masonite 的一个令人敬畏的额外好处。

## 输入数据

Masonite 非常理解输入数据的概念。
再举一个上面 Django 的例子:

```
def upload_file(request):
    if request.method == 'POST':
        firstname = request.POST.get('firstname')
        return HttpResponseRedirect('/success/url/')

    request.GET.get('firstname')
    return render(request, 'success.html', {'key': 'value'}) 
```

注意，我们需要指定我们需要什么类型的输入。这对我来说一直是个奇怪的概念，看起来很奇怪。

Masonite 知道请求方法和你需要什么:

```
class UploadController:
    def show(self):
        firstname = request().input('firstname') # GET
        return view('upload', {'key': 'value'})
    def store(self):
        firstname = request().input('firstname') # POST
        return view('success') 
```

注意，无论是 POST 请求还是 GET 请求，这里的代码都是一样的。