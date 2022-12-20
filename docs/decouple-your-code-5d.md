# 解耦您的代码

> 原文：<https://dev.to/mindsers/decouple-your-code-5d>

解耦你的代码，你的同事会感谢你的。

脱钩并不是一个新概念，但我最近才开始记录自己在这方面的经历。以下是我学到的东西。

## 什么是“脱钩”？

要理解解耦，你首先要知道什么是耦合代码。

当两个具有不同职责的服务需要彼此合作时，就有了耦合的概念。这两个服务是“耦合”的。

我们处理类的方式会增加或减少耦合力:

*   例如，当类在其他类中初始化时，就实现了强耦合。一般来说，直接使用类会增加耦合性。
*   当第一个类**不知道她使用的类**是谁以及如何工作时，就存在弱耦合。

耦合代码示例:

```
<?php

use Config/IniConf;

class IndexController
{
    private $iniConf;

    public function __construct() 
    {
        $this->iniConf = new IniConf('../config.ini');
    }

    public function renderView()
    {
        echo '[' . $this->iniConf->getValue('db') . ' : ' . count($this->iniConf->getValue('db')) . ']';
        echo '[' . $this->iniConf->getValue('name') . ' : 0' . ']';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我的控制器需要访问存储在`.ini`文件中的应用程序参数。没问题，因为我有一个这样做的类:`IniConf`，所以我使用它。我也在其他几个控制器中这样做:`AboutController`、`AdminController`、`PortfolioController`。完美。

但是如果我明天决定改变我的配置文件的地址呢？或者，如果我碰巧想将我的参数存储在数据库中以获得更大的灵活性呢？你知道这是怎么回事吗？每次，我都需要更换控制器。

我必须更改的代码行数可能很快变得极端:我将花费更多的时间来更改我的控制器，而不是编写在数据库中存储配置的类。

## “脱钩”是解决之道

如何解耦代码来限制这种类型的问题？

可以先把`IniConf`和`IndexController`分开。当然`IndexController`需要`IniConf`(它的依赖)才能正常工作，但它不应该初始化它。我们将进行依赖注入:

```
<?php

// Constructeur IndexController
public function __contruct($iniConf) {
    this->iniConf = $iniConf;
} 
```

Enter fullscreen mode Exit fullscreen mode

那里。这样，我可以实例化`IniConf`一次，并将其传递给我的控制器的构造函数。这将限制我需要更改文件地址的地方的数量。

对于我们在更改应用程序的整个参数保存方法时遇到的问题，让我们回顾一下:

第一个类不知道它所使用的类是谁，如何工作以及如何工作。

它需要知道**它需要的类的类型**以及它是如何工作的。为此，我们将在`IndexController`和`IniConf`之间建立一个契约。

根据所用语言的不同，方法也不同:对于 PHP，我们将使用接口，对于 Swift，将使用协议。

```
<?php

// Contrat

namespace Config;

interface Configurator
{
    public function set($key, $value);
    public function get($key);
} 
```

Enter fullscreen mode Exit fullscreen mode

无论我的参数存储方法是什么，我都需要能够访问参数和更改键。你需要创建一个接口`Configurator`来做这件事。

```
<?php

use Config/Configurator;

class IndexController
{
    private $conf;

    public function __construct(Configurator $conf) 
    {
        $this->conf = $conf;
    }

    public function renderView()
    {
        echo '[' . $this->conf->get('db') . ' : ' . count($this->conf->get('db')) . ']';
        echo '[' . $this->conf->get('name') . ' : 0' . ']';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我更改了控制器的代码:

*   构造函数要求对象遵守约定`Configurator`
*   控制器使用契约方法，而不是类的方法。
*   控制器不一定使用`IniConf`，它不知道它实际上使用了什么，我们改变了`$this->conf`中的属性

最后要做的是从`IniConf`开始修改代码，使其遵守契约`Configurator`。

## 结论

代码看起来不错，现在已经解耦了。结果没有任何变化，新代码做的事情与前一个完全一样，但是维护起来更简单。

我的`index.php`代码:

```
<?php

$conf = new IniConf('../config.ini');
$index = new IndexController($conf);
$other = new OtherController($conf);

$index->renderView(); 
```

Enter fullscreen mode Exit fullscreen mode

*依赖注射的妙处。*

我需要更改我的配置文件的地址:

```
<?php

$conf = new IniConf('../libs/config.ini');
$index = new IndexController($conf);
$other = new OtherController($conf);

$index->renderView(); 
```

Enter fullscreen mode Exit fullscreen mode

地址只在一个地方更改，对整个应用程序都有好处。

我在数据库中迁移我的配置。

```
<?php

$conf = new BDDConf([
    'dbname' => 'config',
    'login' => 'dbuser',
    'passwd' => '1234'
]);
$index = new IndexController($conf);
$other = new OtherController($conf);

$index->renderView(); 
```

Enter fullscreen mode Exit fullscreen mode

改变对我的控制器是透明的，我不改变代码。我专注于我的新特性(为新的 stocking 方法编写类),并且没有浪费时间修改出现`IniConf`的每一行代码。

对于那些想更深入了解它的人来说，这里是该项目的 GitHub 链接。