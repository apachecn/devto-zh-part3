# 探索 Craft CMS 3 控制台命令行界面(CLI)

> 原文：<https://dev.to/gaijinity/exploring-the-craft-cms-3-console-command-line-interface-cli-3mg4>

# 探索 Craft CMS 3 控制台命令行界面(CLI)

### Craft CMS 3 附带了许多有用的命令，可以节省您的时间，让您的生活更轻松。在这篇文章中，你将学会如何停止工作并爱上这个网站

安德鲁·韦尔奇

[![Craft-cms-3-console-command-line-interface](img/6c8d62e6980fe64567d10e793de0f4af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dHNqwmXh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-cms-3-console-command-line-interface.jpg)

本文中的要求是截止到**Craft CMS 3.3.16.1**的当前要求

Craft CMS 3 附带了一个非常有用的命令行界面(CLI ),但是有些人担心 CLI 的通用性，而其他人可能并不完全了解 Craft CLI 能为您做什么。对你来说，克服这两者的最好方法是通过理解。

本文深入解释了什么是 Craft CLI，并详细介绍了可供您使用的命令。所以让我们开始吧！

## 我们什么时候会想要使用 Craft CLI？

为了充分理解为什么 Craft CLI 如此有用，我们需要了解一下它是如何工作的，以及常规 web 请求是如何工作的。

[![Learning-how-things-work](img/8b758748e2f6ffd8b5508fa1fac05e87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YMz4LRbt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_992x558_crop_center-center_82_line/2504/learning-how-things-work.jpg)

对于常规的 web 请求，web 服务器处理请求，将 PHP 的处理交给一个单独的进程(通常是 [php-fpm](http://www.cloudways.com/blog/php-fpm-on-cloud/) )。Craft CMS 是用 PHP 编写的，我们编写的 [Twig](http://twig.symfony.com/) tem 模板也是编译成 PHP 的。

所以可以说，对于任何给定的 web 请求，通常都会有相当一部分 PHP 最终运行在我们的 web 服务器上。因为 web 请求是外部的、不可信的请求，所以每个请求都有一个[内存限制](http://php.net/manual/en/ini.core.php#ini.memory-limit)和一个[最大执行时间](http://php.net/manual/en/info.configuration.php#ini.max-execution-time)，这样我们的服务器就不会不堪重负。

虽然这通常不太有用，但当我们需要执行复杂的操作时，如多种图像转换、条目的重新保存，以及特别精心制作的 CMS & plu g in updates，这可能会成为一种障碍。超时发生了，队列作业失败了，这对任何人来说都不好玩。

此外，当我们有机会定期运行任务或对网站进行修改时，手动进入和清除缓存等操作很快就会过时。

Craft CMS con sole command line interface to rescue:

*   通过客户端运行的任务通常没有[内存限制](http://php.net/manual/en/ini.core.php#ini.memory-limit)或[最大执行时间](http://php.net/manual/en/info.configuration.php#ini.max-execution-time)，因为它们是由内部的可信来源运行的，所以它们不会超时或耗尽内存
*   客户端任务可以很容易地定期触发(可能通过 [cron](http://www.ostechnix.com/a-beginners-guide-to-cron-jobs/) 触发),也可以在某些事件发生时以自动方式触发，不需要任何用户干预

因此，对于任何时间的总结或记忆过程，以及当我们需要任务以自动方式完成时，客户端都是我们的朋友。

## Craft CLI 到底是什么？

既然我们已经知道了什么时候可能需要使用 Craft CLI，那么让我们来看看当前内容背后到底是什么。

[![Craft-cms-console-command-line-interface-explained](img/9120299d0d0825cce1c652034eaf5c16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PKxhTUMN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-cms-console-command-line-interface-explained.jpg)

当我们[建立一个新的 Craft CMS 3 项目](http://nystudio107.com/blog/setting-up-a-craft-cms-3-project)时，在项目根目录下有几个有趣的文件:

```
 -rwxr--r-- 1 vagrant vagrant 501 Nov 17 2017 craft
-rwxr--r-- 1 vagrant vagrant 330 Apr 15 2017 craft.bat 
```

`craft.bat`文件只是在窗口上运行脚本的一个包装，它最终运行`craft`命令。所以让我们来看看`craft`命令到底是什么:

```
 #!/usr/bin/env php
<?php
/**
 * Craft console bootstrap file
 */
// Set path constants
define('CRAFT_BASE_PATH', __DIR__ );
define('CRAFT_VENDOR_PATH', CRAFT_BASE_PATH.'/vendor');
// Load Composer's autoloader
require_once CRAFT_VENDOR_PATH.'/autoload.php';
// Load dotenv?
if (file_exists(CRAFT_BASE_PATH.'/.env')) {
    (new Dotenv\Dotenv(CRAFT_BASE_PATH))->load();
}
// Load and run Craft
define('CRAFT_ENVIRONMENT', getenv('ENVIRONMENT') ?: 'production');
$app = require CRAFT_VENDOR_PATH.'/craftcms/cms/bootstrap/console.php';
$exitCode = $app->run();
exit($exitCode); 
```

第一行`#!/usr/bin/env php`被称为 [she bang](https://en.wikipedia.org/wiki/Shebang_(Unix)) (这是在 Ricky Mar tin 之前很久的事情)，它本质上只是告诉控制台脚本是 PHP，并且在当前环境下执行。

其余的代码与我们的`web/index.php`中的非常相似，这是有意义的，因为我们在两种情况下都在运行 Craft。就把`craft`命令想象成网络`index.php`的客户端设备吧。既能启动飞船，又能运行它。

## 在飞船 CLI 中运行宁东西

要以最简单的形式执行 Craft CLI，请确保您位于控制台中项目的根目录下(可能使用 [cd](http://www.tecmint.com/cd-command-in-linux/) 命令),并键入:

```
 ./craft 
```

奇怪的外观是 Unix-ism，意思是“在当前的趋势下”。我知道，我知道。你已经在这个方向上了，为什么它还不知道呢？只是接受它，让它顺其自然，因为如果我们不使用它，我们会看到:

```
 vagrant@homestead ~/sites/craft3 $ craft
-bash: craft: command not found 
```

至于为什么会这样，这是一个很长的故事，但它与 [Unix 路径](http://www.cs.purdue.edu/homes/bb/cs348/www-S08/unix_path.html)的工作方式有关。仅键入`./craft`本身仅列出所有可用的命令(技术术语与`./craft help`相同)。

如果您看到类似这样的`Permission denied`错误:

```
 vagrant@homestead ~/sites/craft3 $ ./craft
-bash: ./craft: Permission denied 
```

这只是意味着工艺脚本没有可执行的错误。您可以用下面的代码来解决这个问题:

```
 chmod a+x craft 
```

有关 Unix 和 Craft Per mission 的更多信息，请查看文章[Harding en ing Craft CMS Per mission](https://dev.to/gaijinity/hardening-craft-cms-permissions-47aj-temp-slug-9032458)。

所以假设我们已经成功地输入了它，我们将会看到这样的东西:

```
 vagrant@homestead ~/sites/craft3 $ ./craft

This is Yii version 2.0.30.

The following commands are available:

- backup Allows you to create a new database backup.
    backup/db (default) Creates a new database backup.

- cache Allows you to flush cache.
    cache/flush Flushes given cache components.
    cache/flush-all Flushes all caches registered in the system.
    cache/flush-schema Clears DB schema cache for a given connection component.
    cache/index (default) Lists the caches that can be flushed.

- clear-caches Allows you to clear various Craft caches.
    clear-caches/all Clear all caches.
    clear-caches/asset Asset caches
    clear-caches/asset-indexing-data Asset indexing data
    clear-caches/compiled-templates Compiled templates
    clear-caches/cp-resources Control Panel resources
    clear-caches/data Data caches
    clear-caches/graphql-caches GraphQL caches
    clear-caches/index (default) Lists the caches that can be cleared.
    clear-caches/route-map Route Map Cache
    clear-caches/temp-files Temp files
    clear-caches/template-caches Template caches
    clear-caches/transform-indexes Asset transform index

- fixture Allows you to manage test fixtures.
    fixture/load (default) Loads the specified fixture data.
    fixture/unload Unloads the specified fixtures.

- gc Allows you to manage garbage collection.
    gc/run (default) Runs garbage collection.

- graphql Allows you to manage GraphQL schemas.
    graphql/dump-schema Dump out a given GraphQL schema to a file.
    graphql/print-schema Print out a given GraphQL schema.

- help Provides help information about console commands.
    help/index (default) Displays available commands or the detailed information
    help/list List all available controllers and actions in machine readable
                                                   format.
    help/list-action-options List all available options for the $action in machine readable
                                                   format.
    help/usage Displays usage information for $action.

- index-assets Allows you to re-indexes assets in volumes.
    index-assets/all Re-indexes assets across all volumes.
    index-assets/one (default) Re-indexes assets from the given volume handle ($startAt = 0).

- install Craft CMS CLI installer.
    install/craft (default) Runs the install migration.
    install/plugin Installs a plugin.

- mailer Allows for testing mailer settings via the CLI.
    mailer/test Allows for the testing of email settings within Craft using one of
                                                   the following scenarios:

- migrate Manages Craft and plugin migrations.
    migrate/all Runs all pending Craft, plugin, and content migrations.
    migrate/create Creates a new migration.
    migrate/down Downgrades the application by reverting old migrations.
    migrate/fresh Truncates the whole database and starts the migration from the
                                                   beginning.
    migrate/history Displays the migration history.
    migrate/mark Modifies the migration history to the specified version.
    migrate/new Displays the un-applied new migrations.
    migrate/redo Redoes the last few migrations.
    migrate/to Upgrades or downgrades till the specified version.
    migrate/up (default) Upgrades the application by applying new migrations.

- project-config Manages the Project Config.
    project-config/rebuild Rebuilds the project config.
    project-config/sync Syncs the project config.

- queue Manages the queue
    queue/exec Executes a job.
    queue/info (default) Info about queue status.
    queue/listen Listens for new jobs added to the queue and runs them
    queue/retry Re-adds a failed job(s) to the queue.
    queue/run Runs all jobs in the queue.

- resave Allows you to bulk-saves elements.
    resave/assets Re-saves assets.
    resave/categories Re-saves categories.
    resave/entries Re-saves entries.
    resave/matrix-blocks Re-saves Matrix blocks.
    resave/tags Re-saves tags.
    resave/users Re-saves users.

- restore Restores a database from backup.
    restore/db (default) Allows you to restore a database from a backup.

- serve Runs the PHP built-in web server.
    serve/index (default) Runs PHP built-in web server.

- setup Craft CMS setup installer.
    setup/db Alias for setup/db-creds.
    setup/db-creds Stores new DB connection settings to the .env file.
    setup/index (default) Sets up all the things.
    setup/security-key Generates a new security key and saves it in the .env file.
    setup/welcome Called from the post-create-project-cmd Composer hook.

- tests Provides support resources for testing both Craft's own services and
                                                   your
    tests/setup Sets up a test suite for the current project.
    tests/test Don't use this method - it won't actually execute anything.

- update Updates Craft and plugins.
    update/composer-install Installs dependencies based on the current composer.json &
                                                   composer.lock.
    update/info Displays info about available updates.
    update/update (default) Updates Craft and/or plugins.

- utils/fix-element-uids Utilities
    utils/fix-element-uids/index (default) Ensures all elements UIDs are unique.

To see the help of each command, enter:

  craft help <command-name> 
```

唷，太多了！这些都是从**Craft CMS 3.3.16.1**开始的内置命令行命令；以后可能会添加更多，插件也可以添加他们自己的命令行！

我们将讨论每个命令的作用，但是最重要的一个命令是:

```
 ./craft help <command-name> 
```

如果我们使用`./craft help`命令作为顶级命令，我们可能会看到类似这样的情况:

```
 vagrant@homestead ~/sites/craft3 $ ./craft help update

DESCRIPTION

Updates Craft and plugins.

SUB-COMMANDS

- update/composer-install Installs dependencies based on the current composer.json & composer.lock.
- update/info Displays info about available updates.
- update/update (default) Updates Craft and/or plugins.

To see the detailed information about individual sub-commands, enter:

  craft help <sub-command> 
```

但是，如果我们对它再熟悉一点，并提供完整的子命令，例如`./craft help update/composer-install`,我们将会看到关于那个 spe cif ic 命令、它所需要的参数等等的更多帮助。:

```
 vagrant@homestead ~/sites/craft3 $ ./craft help update/composer-install

DESCRIPTION

Installs dependencies based on the current composer.json & composer.lock.

USAGE

craft update/composer-install [...options...]

OPTIONS

--appconfig: string
  custom application configuration file path.
  If not set, default application configuration is used.

--color: boolean, 0 or 1
  whether to enable ANSI color in the output.
  If not set, ANSI color will only be enabled for terminals that support it.

--help, -h: boolean, 0 or 1
  whether to display help information about current command.

--interactive: boolean, 0 or 1 (defaults to 1)
  whether to run the command interactively. 
```

我认为`help`命令是最重要的命令，因为我们可以用它来计算如何使用所有其他命令！

让我们来看看可用的命令，以及我们可能用它们做什么。

## 备份

是啊！从 Craft CMS `3.1.21`开始，您现在可以通过一个单独的命令来备份数据库！请看一些[Bran don’t](https://github.com/craftcms/cms/issues/4075)在 FR:
中的试题

```
 # create backup in the given folder
./craft backup ./backups

# create backup in working directory with custom filename
./craft backup backup.sql

# create backup in specific location
./craft backup ./backups/backup.sql

# create backup in the storage/backups/ folder
./craft backup 
```

这可以是一个很好的补充，作为一个 cron 工作或其他自动 mat ed 执行备份工艺数据库！

## 缓存

您可能不会经常使用这个命令，如果有的话。它实际上是从 [Yii2](http://www.yiiframework.com/doc/guide/2.0/en/tutorial-console) 组合而来的，专门处理 Yii2 数据缓存，这在工艺中并不常用(尽管许多插件确实利用了它)。

就缓存和工艺而言，这与你可能想到的东西没有任何关系，这是 ``{% cache %}``标签。关于这方面的更多信息，请查看[的《工艺`{% cache %}`标签》深度文章](http://nystudio107.com/blog/the-craft-cache-tag-in-depth)。

此外，`clear-caches`命令(见下文)可以以任何方式清除 Yii2 数据缓存，所以…这里没什么可看的，继续。

## 清除-缓存

这个`clear-caches`命令是[我给](http://github.com/craftcms/cms/pull/3588)准备的一个技能核心。这与清除缓存的效用完全相同；任何可以从 CP 中清除的东西，现在都可以从 CLI 中清除。

例如，如果您想在将更改部署到网站时清除缓存，这尤其有用。有关这方面的更多信息，请参见下面的 Com pos er Scripts 部分。

**专业提示:**如果你看到错误`Error clearing cache Control Panel resources`，那是因为对于 CLI 请求，你需要告诉 Craft 你的`/cpresources`目录到 ry 在你的`general.php`文件中的位置:

```
 'resourceBasePath' => dirname( __DIR__ ) . '/web/cpresources', 
```

你可以在本期 GitHub 上找到关于上述内容的讨论。

## gc

这个命令有点迟钝，所以似乎只有在某些情况下你才会用到它。如果你使用 Craft 3.1 新的[软删除](http://docs.craftcms.com/v3/extend/soft-deletes.html)特性，并且你想手动或在固定的时间表运行[垃圾收集](http://docs.craftcms.com/v3/gc.html#forcing-garbage-collection)，这是给你的命令。

垃圾收集的作用是永久删除任何被软删除和过期的内容。你可以控制它运行的时间和方式，而不是让它*有机会*运行任何给定的 web 请求(从而有可能减慢 web 请求的速度)。

## 帮助

我们已经谈过这个了。

## 指标-资产

`index-assets`是 Ryan McQuen 的 [PR，它的工作与资产指数实用程序相同，但它是通过 CLI 来完成的。](http://github.com/craftcms/cms/pull/3595)

这可能特别方便，因为资产索引可能需要很长时间，而且通过 CLI 运行比通过 web 请求运行更可靠，因为在索引过程中不会超时或内存不足。

## 安装

`install`命令可以用来安装 Craft 本身(它运行建立数据库结构的安装迁移)或安装一个插件。要安装一个 plu g in，它需要已经在你的`composer.json`中，但是这给你一个从 CLI 完成的方法，可能是你的沸腾板设置的一部分。

如果你想安装 Craft 作为某个更大的自动装配过程的一部分，你可以非主动地运行它，例如:

```
 ./craft install \
    --interactive=0 \
    --email="admin@example.com" \
    --username="admin" \
    --password="secret" \
    --siteName="Craft3" \
    --siteUrl="$SITE_URL" \
    --language="en" 
```

**注意:**命令，这是在 Unix shells 中使用多行命令的一种通用方式。

查看[设置一个新的 Craft CMS 3 项目](http://nystudio107.com/blog/setting-up-a-craft-cms-3-project)文章，了解更多关于设置你自己的 Craft CMS 锅炉板设置的信息。

## 迁移

`migrate`命令让您可以控制自己的迁移。这里最常见的用例是将迁移作为自动化构建部署的一部分来运行，因此当您将更新推送到生产环境中来制作 CMS 或插件时，您不必争着使用`/admin` CP 来运行迁移。

在本文中，“迁移”是指一组数据库命令，Craft 和/或 plu g ins 运行这些命令来改变数据库的结构。随着功能的增加或缺陷的修复，这是不时需要的。

如果 Craft 或 plu g ins 有挂起的迁移，那么在迁移运行之前，您的网站将无法对公众开放。

没有办法预测 Craft 或 plu g ins 何时需要进行迁移，所以最好在每次更新时自动完成。有关这方面的更多信息，请参见下面的 Com pos er Scripts 部分。

## 工程图

如果你正在使用 Craft CMS 3.1 的新的[项目配置](http://docs.craftcms.com/v3/project-config.html)特性，那么`project-config/sync`又是一个你最喜欢在部署过程中运行的命令。

如果您在 Craft 3.1 中使用 Project Con fig，并且已经推送了对`project.yaml`的更改，那么在 Project Con fig 同步之前，您的网站将无法访问公共许可证。

避免与`/admin` CP 的快速竞争，以确保您的`project.yaml`变化在您推动它们时应用到生产中。有关这方面的更多信息，请参见下面的 Com pos er Scripts 部分。

Craft CMS 3.1.20 中的另一个新特性是`project-config/rebuild`命令，它将在某些东西不同步的情况下从数据库中重建你的`project.yaml`。

## 队列

queue 命令允许您通过 CLI 与 Craft CMS 队列和队列作业交互。如果您遇到阻塞队列作业的问题(如上所述，通过 web 请求运行)，请查看 Craft CMS 中的[健壮队列作业处理文章。](https://nystudio107.com/blog/robust-queue-job-handling-in-craft-cms)

然后您可以关闭[运行队列来调用](http://docs.craftcms.com/v3/config/config-settings.html#runqueueautomatically)配置设置，并通过`./craft queue/listen`通过 CLI 在后台运行队列

如果你不想走这么远，Oliv er Stark 的 [Async Queue plu g in](http://github.com/ostark/craft-async-queue) 可以给你同样的结果。

## 版

该命令是最新的 CMS CLI 命令。它再次允许您通过 CLI 运行冗长且记忆深刻的“重新保存元素”任务。[参见本期 GitHub](http://github.com/craftcms/cms/issues/3482)了解背景。

我认为我们在这里看到了一种模式，这种模式将 CLI 用于冗长和/或内存密集型操作。

## 发球

`serve`命令是来自马克·霍特的 [PR，确保](http://github.com/craftcms/cms/pull/3770) [Yii2 serve](http://www.yiiframework.com/doc/guide/2.0/en/start-installation#verifying-installation) 命令与 Craft CMS 一起工作。那是什么意思？

这意味着在零本地开发环境下，您可以从给定的主机/端口开始创建服务器。方便在一瞬间安装和运行物品！

## 设置

`setup`命令允许你为环境设置建立你的`.env`文件，包括数据库设置和其他“秘密”或因环境而异的东西。

这在[工艺安装说明](http://docs.craftcms.com/v3/installation.html)中有详细介绍，并且这是一个很好的交互方式来填充需要的内容，而不必创建&用编辑器直接编辑`.env`文件。

## 更新

这个`update`命令是一种减轻通过 CP 运行 Com pos 的痛苦的方法，因为 Pix el & Ton ic 在他们的[更新服务](http://craftcms.com/blog/state-of-craft-2018)上工作。这一条值得注意子命令明确做了什么:

*   `update/update` —这与点击**更新**做的事情完全一样，但是在 CP 中，但是通过 CLI 运行它，所以你不必担心计算机超时或内存不足
*   `update/composer-install` —这是你在部署 Craft CMS 时使用的一些东西，有效地做与`composer install`相同的事情，但是对于没有安装 Com pos er 的服务器设置。

如果您没有在本地或您的服务器上安装 Com pos 机(或者只是不想处理 Com pos 机)，但是遇到了通过 CP 更新的问题，这些命令是有用的。

如果您希望部署过程像在[建立一个新的 Craft CMS 3 项目](http://nystudio107.com/blog/setting-up-a-craft-cms-3-project#deploying-craft-cms)文章的**部署 MENT** 部分中所讨论的那样自动进行，这也是有用的，但是同样，不要让 Com pos 对您有用。

## 用 pos er 脚本编写

您可以通过 shell 脚本或类似的方式直接运行这些命令，作为部署过程的一部分，但是您也可以利用 age Composer。

电脑有一个脚本的概念，可以在各种事情发生时执行。例如，假设您在本地开发中更新了 Craft CMS 站点，然后将`composer.lock`文件推送到生产中。

在生产环境中，您可以运行`composer install`命令，并且在本地开发中测试的&将被安装到生产环境中。

你可以在你的`composer.json` :
的`"scripts"`部分添加一些类似的东西

```
 "scripts": {
        "post-root-package-install": [
            "@php -r \"file_exists('.env') || copy('.env.example', '.env');\""
        ],
        "post-create-project-cmd": [
            "@php craft setup/welcome"
        ],
        "post-update-cmd": [
            "@php craft migrate/all",
            "@php craft project-config/sync",
            "@php craft clear-caches/all"
        ],
        "post-install-cmd": [
            "@php craft migrate/all",
            "@php craft project-config/sync",
            "@php craft clear-caches/all"
        ]
    } 
```

这意味着任何时候运行`composer update`或`composer install`，它都会运行 Craft CLI 命令:

*   运行所有挂起的迁移
*   同步您的`project.yaml`更改
*   清除所有缓存

嘣，瞬间自动垫展开。好的一面是，它也可以在本地 dev 中运行。

**专业提示:**如果您在 [buddy.works](http://buddy.works/) 容器或类似的容器中执行`composer install`，在那里您不希望运行迁移(可能根本没有数据库)，您可以使用:

```
 composer install --no-scripts 
```

这样，您的`composer install`将会打开，但是没有一个计算机脚本会在它们可能无法正常执行的环境中运行。

## 把乒包起来

她就写了这么多！希望这篇文章已经帮助 demys 对 Craft CMS 客户端进行了一些改进，并为如何使您的开发和部署过程变得更好提供了一些思路。

[![Have-no-fear-of-the-cli](img/bcfaccf86b1477b172896d617c34a4a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qmaTUA7o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_768x432_crop_center-center_100_line/have-no-fear-of-the-cli.jpg)

不要害怕 CLI 就在这里！拥抱它，它可以帮助你自动配对，让你的生活更轻松。

快部署！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计