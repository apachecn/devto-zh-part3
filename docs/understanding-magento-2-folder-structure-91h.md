# 了解 Magento 2 文件夹结构

> 原文：<https://dev.to/waceeran/understanding-magento-2-folder-structure-91h>

在本文中，我将带您了解 Magento 2 的文件夹结构以及如何在其中导航。如果你来自 Magento 1，知道在哪里添加你的模块，或者添加一个自定义主题来改变你的商店外观，一开始可能会令人生畏。

从这个链接[https://magento.com/tech-resources/download](https://magento.com/tech-resources/download)下载任何 Magento 版本，从[https://dev docs . Magento . com/guides/m1x/install/installing _ install . html](https://devdocs.magento.com/guides/m1x/install/installing_install.html)获取安装说明。

**app/**
该文件夹默认包含 design、i18n 等三个子目录。app/code//目录应该包含您创建的所有自定义模块。还建议将您所有的第三方模块存储在这里。

您的自定义商店主题应该存储在 app/design/frontend 子目录下，而 app/design/adminhtml 子目录应该用于存储您的所有管理主题。

app/i18n 目录存储语言包。如果您想创建一个多语言商店来满足客户需求，您可能需要使用此文件夹。

全新安装后，代码子目录将会丢失。所有 Magento 核心模块都在供应商目录中，以避免您的自定义模块与 Magento 核心模块混淆。与 Magento 1 中必须将所有 Magento 核心模块提交给 Git 库不同，Magento 2 中两者是分开的。

存储通过 Composer 安装的所有软件包，包括 Magento 核心模块。从这个文件夹工作是不可取的，因为一旦你升级了你的 Magento 厂商文件，你的所有更改都将被覆盖。强烈建议扩展 app/code/或 app/design 下的模块。

**phpserver/**
该文件夹包含 Router.php 文件，其主要用途是实现 PHP 内置服务器。不鼓励使用该文件，因为它存在大量潜在的安全漏洞。请参见自述文件

**bin/**
该目录包含 Magento CLI 可执行脚本。这个脚本激活 Magento 命令，帮助清除缓存，重建索引等。

**setup/**
包含 Magento 的安装设置文件。

**生成/**
存储 Magento 生成的所有代码。默认情况下，如果类被注入到构造函数中，Magento 会生成代码来创建不存在的工厂类。

**var/**
包含生成的类、缓存、会话、数据库备份和缓存的错误报告。这个文件夹有许多子目录，包括 var/di，一旦运行 php bin/magento setup:di:compile，就会生成其内容。

var/log 包含 magento 日志文件，主要是 exception.log 和 system.log 文件。

var/cache 存储 Magento 的所有缓存。为了看到您的开发变化，建议使用 php bin/magento cache:clean 命令持续清除您的缓存

**dev/**
包含 Magento 测试框架运行的自动化功能测试。

**lib/**
存储所有 Magento 和厂商库文件。它还包含所有基于非模块的 Magento 代码。这主要是帮助 Magento 运行的系统代码。

**pub/**
该目录还包含用于在生产模式下运行应用程序的 anindex.php 文件。pub 目录提供了一种安全措施，从而限制公众对根目录资产的访问。这个文件夹也包含了你的 Magento 主题的静态文件。

**结论**
至此，我相信你已经理解了 Magento 2 的文件夹结构以及每个文件夹中的内容。这将帮助您开始使用 Magento 2，并在短时间内轻松定制您的商店。您可以随时参考 Magento 官方文档，更深入地了解不同 Magento 组件之间的交互方式。