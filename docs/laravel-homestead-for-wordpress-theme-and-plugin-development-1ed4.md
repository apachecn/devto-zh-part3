# WordPress 主题和插件开发的 Laravel 家园

> 原文：<https://dev.to/kasparsd/laravel-homestead-for-wordpress-theme-and-plugin-development-1ed4>

原来 [Laravel Homestead](https://laravel.com/docs/5.7/homestead) 几乎就是[我一直在寻找的开发环境](https://kaspars.net/blog/wordpress/wordpress-environment-composer)——它[可以作为 Composer dependency](https://laravel.com/docs/5.7/homestead#per-project-installation)添加到任何 PHP 项目中，并使用[一个简单的 Yaml 文件](https://laravel.com/docs/5.7/homestead#configuring-homestead)进行配置。主机只需要[流浪汉](https://www.vagrantup.com/)和 [VirtualBox](https://www.virtualbox.org/) 。

**TL；dr:** 参见[这个拉取请求](https://github.com/kasparsd/widget-context-wporg/pull/48)，了解我如何将 Homestead 添加到[小部件上下文插件](https://widgetcontext.com/)中。

## 工作原理

首先，我们将一个非常小的`Vagrantfile`添加到项目根中，它从`Homestead.yaml`(可以是任何名称)中读取 Homestead 的配置，并使用提供的配置触发`scripts/homestead.rb`中的供应逻辑。

我们在`package.json`中安装了 WordPress a 开发依赖项，并使用内联 shell 脚本(和与 Homestead 捆绑在一起的[WP-CLI](https://wp-cli.org/))
从同一个`Vagrantfile`中配置它

```
config.vm.provision "shell",
    inline: "wp config create",
    privileged: false 
```

并使用专用的`wp-cli.yaml`来定义数据库访问参数和凭证:

```
path: /home/vagrant/code

config create:
  dbname: homestead
  dbuser: homestead
  dbpass: secret 
```

在`wp config create`期间用作默认值。

注意，`wp-cli.yaml`位于我们的主题目录中，所以我们在`Homestead.yaml`中指定了`WP_CLI_CONFIG_PATH`环境变量，它指向虚拟环境中的`wp-cli.yaml`。

## 备注

Laravel Homestead 在虚拟机内部以`root`的身份运行供应脚本，因此普通的非特权用户`vagrant`不能写入磁盘，这阻止了我们从虚拟环境中下载和设置 WordPress。这也许可以通过`Homestead.yaml`中的几行附加配置来调整。