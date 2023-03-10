# Drupal 和 Particle via Composer

> 原文：<https://dev.to/jcandan/drupal-and-particle-via-composer-3lj9>

如果你想在 Drupal 上使用 Pattern Lab，并且想要一个带有 Bootstrap 4 和许多其他好东西的固执己见的初学者工具包，那么看看 Phase2 的 [Particle](https://phase2.gitbook.io/frontend/) 。

[设计系统优先开发的实践](https://www.youtube.com/watch?v=uYQjN9Ydgqk)将带你开始使用 Particle。

## 为什么要这样引导？

[Particle >入门> Drupal 8](https://phase2.gitbook.io/frontend/particle/getting-started/drupal-8) 文档指导我们下载 Particle 到`themes`目录。但是您更喜欢使用 Composer 来管理 Drupal 8 核心和贡献模块。下面的指南将带你了解如何用粒子做到这一点。

> 注意:使用 Composer 对于我们使用 Drupal 8 获得 Particle 来说并不是必须的，事实上，它有一个缺点，除了最坚定的人之外，其他人可能会对它望而却步。

## 告诫

Christopher Bloom 在 Drupal Slack 上的一个帖子中指出，

> “Particle 不是一个可以在系统重建时安全地重新安装的库，而是一个必须提交给 repo 的初学者工具包。”

这意味着如果您走这条路，您将需要通过 Composer 应用的补丁来管理对粒子的定制。为什么？因为，否则，当你`composer update`的时候，你会覆盖你所做的任何改变。对于大多数考虑通过 Composer 管理粒子的人来说，这可能是一个障碍。

如果你选择不和作曲家一起去，我理解。我自己对此仍持观望态度。但是，至少，我确信许多 Drupalers 将从下面的节点版本部分中受益。

## 第一件事，节点版本

在撰写本文时，Particle 支持节点版本 6、8 和 10。节点版本为 12。因此，如果您需要在您的机器上支持多个版本的 Node，我推荐以下内容:

*   卸载节点

如果已经安装了`node`,卸载它以便`nvm`可以全局或按项目管理您的活动版本。

*   安装节点版本管理器。

我成功地使用了`brew install nvm`，但是还有[个额外的指令](https://medium.com/@isaacjoe/best-way-to-install-and-use-nvm-on-mac-e3a3f6bc494d)你需要遵循。

*   安装节点 10。

您可以用`$ nvm ls-remote`列出可用版本。注意，v10.x 最新的 LTS(长期支持)被命名为 Dubnium。

```
$ nvm install --lts=dubnium 
```

## 通过 Composer 获得粒子

我们假设您已经有了一个基于`drupal-composer/drupal-project`的工作 Drupal 实例。

*   获取组件模块

Particle 不是典型的 contributed Drupal 主题，没有列出这个模块依赖。所以，你必须自己通过 Composer 得到它。

```
$ composer require drupal/components
$ drush en components 
```

*   在`composer.json`中添加安装路径

这告诉 Composer 将包下载到指定的目录。

```
 "extra": {
        "installer-paths": {
            "web/themes/{$name}": ["phase2/particle"],
            ...
        }
    } 
```

> 注意:我们采用了指令中建议的路径，而不是`themes/contrib`或`themes/custom`，因为这个设计系统需要提交到版本控制中。

*   需要粒子包

```
$ composer require phase2/particle 
```

## 构建并安装

*   使用`nvm`，在`web/themes/particle`目录中指定项目特定的节点版本。

```
$ echo "10.15.3" > .nvmrc 
```

*   从`{drupal-root}/themes/particle/`内运行:

```
$ npm install
$ npm run setup
$ npm run build:drupal 
```

*   安装粒子主题并设为默认

```
$ drush theme:enable particle
$ drush config-set system.theme default particle 
```

## 结论

现在，您在 Drupal 中有了一个工作粒子主题。此时，您可以继续遵循快速开发模式配置底部的[入门> Drupal 8](https://phase2.gitbook.io/frontend/particle/getting-started/drupal-8) 指令。

同样，在选择这个选项之前，请确保您理解上述警告。