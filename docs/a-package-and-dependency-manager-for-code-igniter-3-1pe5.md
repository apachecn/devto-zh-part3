# Code Igniter 3 的包管理器

> 原文：<https://dev.to/francis94c/a-package-and-dependency-manager-for-code-igniter-3-1pe5>

Code Igniter 是一个简单而强大的 PHP MVC 框架。有了它，人们可以用普通 PHP 编写相同应用程序所需的一半时间来构建功能丰富的 web 应用程序，同时还能解决安全性问题。

我使用 Code Igniter 大约 3 年了，我知道它的核心非常高效，是最简单的 MVC 框架之一。

虽然小而简单，但它的应用范围从简单到非常复杂的项目。

到目前为止，我能想到的原因只有两个，即这个框架多年来一直缺乏促使人们选择它的替代品(Laravel)的东西。

第一个原因是它无法适应 PHP 推出的新的 [PSR-4 自动加载](https://www.php-fig.org/psr/psr-4)特性。当您试图加载与 Code Igniter 3 为适应 PHP 5.3 而构建的相同类名但在不同目录中时，您会注意到这一点。+并且有一个内置的预防机制来避免类名冲突。

下面控制器中的代码将只加载分配给 alias1
的库

```
<?php
defined('BASEPATH') OR exit('No direct script access allowed');
class MainController extends CI_Controller {

  function index() {
    $this->load->library("nodes/Lib", null, "alias1");
    $this->load->library("Lib", null, "alias2");
  }
}
?> 
```

这个问题目前正由处于测试阶段的 Code Igniter 4 解决。

第二个原因是，与 Laravel 不同，Code Igniter 没有像 [Packagist](https://packagist.org/) 那样可以为您节省大量开发时间的包存储库。

你可以从 GitHub 获得库，但是在使用它们之前，你仍然需要自己修改/复制它们到正确结构的正确目录中，以适应 Code Igniter。

您可能会发现，一些库实现了 Code Igniter 中已经存在的功能，因此重新发明了轮子。如果你是编写库的人，除非必要，否则你不会想这么做。

[![Splint Logo](img/40f3616a93aab18bf6c38a274bf0f463.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CNKg2pni--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nk8q2mp71zh5v934r6rn.png)

Splint 是一个专门为 Code Igniter 构建的包和依赖管理器，允许构建迷你应用程序，这些包可以利用 Code Igniter 加载器来加载资源，如视图、模型、配置、库等。无论你能在 Code Igniter 应用中做什么，你也能在 Splint 包中做。

在 Splint 包中加载和使用库就像下面显示的一样简单。

```
<?php
$this->load->splint("francis94c/ci-preference", "+CIPreferences", null, "prefs");
$this->prefs->get("key");
?> 
```

如您所见，默认情况下，splint()函数不是 loader 类的成员。但是，您需要从应用程序/核心文件夹中修改 Loader 类。每当你安装一个软件包时，夹板终端工具将自动为你做这件事。

split 有一个 [Wiki](https://splint.cynobit.com/wiki) ,在那里你可以学习如何构建 split 包。然而，夹板仍然是非常新的，有很少的软件包，你可以利用。您可以随时访问他们的网站，查看您可以安装的软件包。

使用 Splint Terminal 工具安装软件包就像使用下面的命令一样简单。

```
splint install vendor/package_name 
```

[split](https://splint.cynobit.com)也有一个 [SDK](https://github.com/splintci/sdk) ，它实际上是一个完整的 Code Igniter 发行版，它的加载器已经修改过了，还有一个平台包，可以在开发/测试包时使用。

> 所有的包必须在 GitHub 上公开托管，然后才能导入到[split](https://splint.cynobit.com)中。

### 一个样品夹板包

让我们看看我为简单键值对的持久性构建的夹板包。我将这个包称为 ci-preference，它托管在 [GitHub](https://github.com/francis94c/ci-preference) 上。你可以访问它的夹板页[这里](https://splint.cynobit.com/packages/francis94c/ci-preference)。

这个库使用两个主要函数 set()和 get()来设置值和通过键获取值。

要在 Code Igniter 中使用这个库，您只需下载 split terminal[tool](https://splint.cynobit.com/downloads/splint)并运行 split install Francis 94 c/ci-preference，在您的 Code Igniter 发行版中安装这个包。

然后，在任何控制器/模型中，使用其自述文件中描述的包，您可以在该包的 split[页面](https://splint.cynobit.com/packages/francis94c/ci-preference)或 GitHub 页面上找到该包。

```
<?php
defined('BASEPATH') OR exit('No direct script access allowed');
class A_Controller extends CI_Controller {

  function index() {
    $params = array();
    $params["file_name"] = "preferences"; // File name of the preference file.
    $this->load->splint("francis94c/ci-preference", "+CIPreferences", $params, "alias");
    $this->alias->get("key", "defaultVal");
    $this->alias->set("key", "val");
    $this->alias->increment("key1", 5) // Increments 'key1' by 5.
    $this->alias->increment("key1") // increments 'key1' by 1.
    $this->alias->concat("key2", "A") // Concatenates 'A' to 'key2'.
    $this->alias->commit(); // Commits all values to file.
  }
}
?> 
```

就这些了。如果您对想要在 Code Igniter 中构建和使用的库有什么想法，请查看 Splint 的开发人员文档。

您还可以修改其他库/包来适应 Code Igniter，并将其发布/导入到 Splint，因为这肯定会对 Code Igniter 社区有很大帮助。