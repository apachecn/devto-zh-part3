# 从 dynaconf 导入设置

> 原文：<https://dev.to/rochacbruno/from-dynaconf-import-settings-2f8o>

通常，当开始一个新的 Python 项目时，我们需要花一些时间来考虑如何管理设置，决定在哪个模块上编写配置管理器，决定给这个模块起什么名字，创建一个类或函数来存储配置密钥，为多个环境创建条件，并且仍然需要考虑这些密钥将存储在哪里以及以何种文件格式存储？

不再有了！现在你有了 [Dynaconf](http://github.com/rochacbruno/dynaconf) ！

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [罗恰布鲁诺](https://github.com/rochacbruno) / [ dynaconf](https://github.com/rochacbruno/dynaconf)

### Python ⚙的配置管理

<article class="markdown-body entry-content container-lg" itemprop="text">

[![dynaconf. new logo](img/431a4b1b6ee8a4dc34fbece266f0d63a.png)](https://github.com/rochacbruno/dynaconf/art/header.png?v2)

> **dynaconf**-Python 的配置管理。

[![MIT License](img/5ac64c708e69b07d53c92cbeccea29d2.png)](https://github.com/rochacbruno/dynaconf/LICENSE)[![PyPI](img/28159831d2e19b26d489c366199da1e8.png)](https://pypi.python.org/pypi/dynaconf)[![PyPI](img/740a7fa53a2d91b565a120ac27808119.png)](https://github.com/rochacbruno/dynaconf)[![PyPI - Downloads](img/b5894db7f2ec50d02d340481c61023c6.png)](https://camo.githubusercontent.com/10a7b30bff3d734d0e3fbdf72551f82fb915fbb6107d5742ce92d154d7209c3e/68747470733a2f2f696d672e736869656c64732e696f2f707970692f646d2f64796e61636f6e662e7376673f6c6162656c3d706970253230696e7374616c6c73266c6f676f3d707974686f6e)[![CI](img/6e5fd734c1fa45eb584a753227871f1a.png)](https://github.com/rochacbruno/dynaconf/actions/workflows/main.yml)[![codecov](img/f911920372c3cc1321839e33ea2d9120.png)](https://codecov.io/gh/rochacbruno/dynaconf)[![Codacy Badge](img/d974949918fa334c7ed4520fe68018a9.png)](https://www.codacy.com/gh/rochacbruno/dynaconf/dashboard?utm_source=github.com&utm_medium=referral&utm_content=rochacbruno/dynaconf&utm_campaign=Badge_Grade)[![GitHub issues](img/0d9863bcd9bc61b4c1eb349260b91e27.png)](https://camo.githubusercontent.com/73245d4e818c86a500282f8ddaa8726457124e57f9b71f72e4962a4842f924f3/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6973737565732f726f636861636272756e6f2f64796e61636f6e662e737667)[![GitHub stars](img/815748c2ea224697234269c59a52c173.png)](https://camo.githubusercontent.com/836cb01b8e9525f2fa24a61fbba434b1fa9d65d5865d1430d940cc3cdd6cd744/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f73746172732f726f636861636272756e6f2f64796e61636f6e662e737667)[![GitHub commits since latest release](img/0f1559aca5cea3f85b2258037ea7252a.png)](https://camo.githubusercontent.com/a2540121058fcbb6baaa7c235165b5dda6d579babf23673f11fb04ab11b7794b/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f636f6d6d6974732d73696e63652f726f636861636272756e6f2f64796e61636f6e662f6c61746573742e737667)[![GitHub last commit](img/fb9a4c3d37da2f8ff24148094e0a4fe5.png)](https://camo.githubusercontent.com/b747586104e578d336f5b9cc02678f7847033c5df555026a8f84d2bb7162bb0e/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6173742d636f6d6d69742f726f636861636272756e6f2f64796e61636f6e662e737667)[![Code Style Black](img/1abbac1bb267dc041dcfa091baa4840f.png)](https://github.com/ambv/black/)[![Discussion](img/11fd0e74490e98022f77824215df3c69.png)](https://github.com/rochacbruno/dynaconf/discussions)[![Discussion](img/98b099e97de02a889e6bbe59131aa990.png)](https://github.com/rochacbruno/learndynaconf)

[![Foo](img/57765dea5e18d03d9846c4dcf2ab9f97.png)](https://xscode.com/rochacbruno/dynaconf)

## 特征

*   受 [12 因素应用指南](https://12factor.net/config)的启发
*   设置管理(默认值、验证、解析、模板化)
*   保护敏感信息(密码/令牌)
*   多种文件格式`toml|yaml|json|ini|py`和可定制的加载器。
*   完全支持环境变量覆盖现有设置(包括 dotenv 支持)。
*   适用于多种环境的可选分层系统`[default, development, testing, production]`
*   内置支持哈希公司保险库和 Redis 作为设置和秘密存储。
*   Django 和 T2 的内置扩展。
*   CLI 进行`init, list, write, validate, export`等常用操作。
*   关于[https://dynaconf.com](https://dynaconf.com)的完整文档

## 快速启动

> **演示:**你可以在这里看到一个工作演示:【https://github.com/rochacbruno/learndynaconf】T2

### 安装

```
$ pip install dynaconf
```

Enter fullscreen mode Exit fullscreen mode

#### 在项目根目录上初始化 Dynaconf

```
$ cd path/to/your/project/
$ dynaconf init -f toml

⚙️  Configuring your Dynaconf environment
------------------------------------------
🐍 The file `config.py` was generated.

🎛️  settings.toml created to hold your settings.

🔑 .secrets.toml created to hold your secrets.

🙈 the .secrets.*
```

…</article>

[View on GitHub](https://github.com/rochacbruno/dynaconf)

将您宝贵的时间花在开发应用程序上，运行`pip install dynaconf`并让 Dynaconf 来处理您的设置。

## 快速启动。

```
from dynaconf import settings 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

这是您需要的唯一一行代码，没有复杂的样板文件，没有 hadouken-ifs，也不需要维护配置类。

你一定在想- **“这是什么魔法？设定值从哪里来？”**

嗯，没有魔法，默认情况下，值可以来自您想要的任何地方，并遵循 的建议，Dynaconf 优先选择环境变量。

```
# optionally you can save it in .env file
export DYNACONF_DEBUG=true
export DYNACONF_NAME=Bruno 
```

Enter fullscreen mode Exit fullscreen mode

```
# app.py from dynaconf import settings
if settings.DEBUG is True:
    print(settings.NAME) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ python3 app.py
Bruno 
```

Enter fullscreen mode Exit fullscreen mode

> Dynaconf 的环境变量是使用`toml`格式输入的，所以`true`已经被评估为布尔`True`，这使得导出列表、字典、浮点、布尔等等成为可能。

[阅读更多关于 envvars 的信息](https://dynaconf.readthedocs.io/en/latest/guides/environment_variables.html)

## 多环境变量

嗯，那很酷，但是你的项目不会有仅仅来自环境变量的设置，我确信你想有一个设置文件，在那里你可以设置默认值。

Dynaconf 可以读取多种文件格式，开箱后它支持`.py`、`.toml`、`.ini`和`.json`。如果安装了`PyYAML`,那么它也将支持`.yaml`,你不必去寻找和打开文件。首选格式是`.toml`,因为它是目前被广泛采用的最佳配置格式，您可以使用任何想要的文件格式。

```
# settings.toml
[default]
name = "Bruno"

[development]
debug = true

[production]
debug = false 
```

Enter fullscreen mode Exit fullscreen mode

```
# app.py from dynaconf import settings
if settings.DEBUG is True:
    print(settings.NAME) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ python3 app.py
Bruno 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，现在使用`settings.`文件，我们可以有单独的`[environments]`默认情况下，dynaconf 将始终在`[development]`上工作，这意味着只有`[default]`和`[development]`变量将被加载。您可以随时执行`export ENV_FOR_DYNACONF=production`，然后它开始使用来自`[production]`环境的值。

> 如果你不想按环境来划分，你可以简单地把所有东西放在`[default]`部分。

阅读关于[环境和设置文件](https://dynaconf.readthedocs.io/en/latest/guides/usage.html#working-environments)的更多信息

## 有些值是秘密

一个好的做法是不要把你的**秘密**像**密码**和**令牌**直接存储在设置文件上，因为你可能会犯错误并把它提交给一个公共的 git 存储库，所以有一些替代方法来存储**秘密**

### 环境变量

> 不推荐

有一些人[不同意](https://diogomonica.com/2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/)，这确实是一个安全失败的点。但是，如果你确定你的机器是受保护的，你可以把秘密留在变量中，风险自负，Dynaconf 可以正常读取它。

### 秘密文件

这是一个简单的保密安全级别，保密`development`特别有用。你用来访问开发 API 等的那个**令牌**。

这很简单，你把一个名为`.secrets.toml`的新文件和你正常的`settings.toml`放在一起，并在那里存储你的敏感数据。Dynaconf 将在读取`settings.toml`之后读取它

等待..它如何解决我的安全问题？

现在还没有，但是它在两个方面让你的生活变得更容易。

1.  将`.secrets.*`放在`~/.gitignore`中，这样你就不会犯将数据发送到公共 git 存储库的错误。
2.  当`DEBUG_LEVEL_FOR_DYNACON=DEBUG`被导出时，Dynaconf 可以输出调试信息，所有加载的值都被打印出来，但是如果这些值来自于`.secrets.*`文件，那么只有键被打印出来，值被屏蔽。在公共配置项上使用很有用。
3.  您可以在 Ansible 部署行动手册上设置一个步骤，该步骤将安全地复制或生成机密文件，并存储在您的生产环境中。

您还可以告诉 Dynaconf 从其他地方加载该文件`export SECRETS_FOR_DYNACONF=/path/to/secrets/location/.secrets.yaml`(对于像 Jenkins 这样的 CI 非常有用)

### 来自 Hashicorp 的  Vault 项目

> 推荐！

现在我们真正谈论的是真正的安全

使用 **Vault** 是保护你的秘密的更好方法 dynaconf 有内置支持:

```
export VAULT_ENABLED_FOR_DYNACONF=true
export VAULT_URL_FOR_DYNACONF=https://..../
export OTHER_VAULT_CONFIGS 
```

Enter fullscreen mode Exit fullscreen mode

然后，如果您的 vault 服务器上有`TOKEN`存储，您可以简单地执行:

```
from dynaconf import settings
perform_your_authentication(settings.TOKEN) 
```

Enter fullscreen mode Exit fullscreen mode

保险库有许多功能，如租赁和密封保险库。

[阅读更多](https://dynaconf.readthedocs.io/en/latest/guides/sensitive_secrets.html)

## 用的是 Django 还是 Flask？

Dynaconf 为这两个框架提供了扩展，您只需用两行代码启用它，然后您的框架将开始从 Dynaconf 读取设置。

### 姜戈

```
# settings.py import dynaconf  # noqa settings = dynaconf.DjangoDynaconf(__name__, **options) 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以通过`django.conf.settings.FOO`进入你的应用程序

[阅读更多](https://dynaconf.readthedocs.io/en/latest/guides/django.html)

### 烧瓶

```
# app.py from dynaconf import FlaskDynaconf
FlaskDynaconf(app, **options) 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以通过`app.config['FOO']`进入你的应用程序

[阅读更多](https://dynaconf.readthedocs.io/en/latest/guides/flask.html)

## 如果使用的是不同的设置文件格式呢？不同的框架和不同的外部存储？

> 您可以添加新的加载程序来扩展 Dynaconf！

Dynaconf 已经为以下产品提供了加载器:

*   `.py`
*   `.json`
*   `.yaml`
*   `.toml`
*   `.ini`
*   重定向服务器
*   Vault 服务器
*   `.env`文件
*   环境变量

但是如果这不适合你的项目，你仍然可以[创建你自己的加载器](https://dynaconf.readthedocs.io/en/latest/guides/extend.html)

## 结论

> Dynaconf 是您管理设置所需的唯一工具！

*   久经考验
*   受到 Red Hat、Seek、Catho 等公司的信任
*   在 Linux 和 Windows 环境下均经过良好测试
*   将设置与代码严格分离(遵循 [12 因素应用](https://12factor.net/config)指南)。
*   定义综合默认值。
*   以多种文件格式(**)存储参数。toml** ，。json，。yaml，。ini 和。py)。
*   令牌和密码等敏感的**机密**可以存储在`.secrets`文件或`vault server`等安全的地方。
*   可以选择将参数存储在 Redis server 等外部服务中。
*   简单的**特征标志**系统。
*   分层**【环境】**系统。
*   环境变量可用于覆盖参数。
*   支持`.env`文件来自动导出环境变量。
*   正确的数据类型(即使是环境变量)。
*   让**只有一个**规范设置模块来统治你所有的实例。
*   为**烧瓶** `app.config`对象添加扩展。
*   为 **Django** `conf.settings`对象添加扩展。
*   强大的 **$ dynaconf** CLI，帮助您通过控制台管理设置。
*   可定制的**验证**系统，确保正确的配置参数。
*   允许动态更改 **dyna** mic 参数，无需重新部署您的应用程序。

## 阅读文档

> 设置很简单，但 Dynaconf 提供了更多的功能，如**功能标志**、**设置上下文管理器**、**插件设置**等..

**文件**:【http://dynaconf.readthedocs.io/ T2】

Dynaconf 正在等待您的[反馈](https://github.com/rochacbruno/dynaconf/issues/155)和[贡献](https://github.com/rochacbruno/dynaconf/issues)

:)

```
from dynaconf import settings
settings.THANKS_FOR_READING 
```

Enter fullscreen mode Exit fullscreen mode