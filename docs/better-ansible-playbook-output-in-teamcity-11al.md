# 在 Teamcity 中更好地翻译剧本输出

> 原文：<https://dev.to/dploeger/better-ansible-playbook-output-in-teamcity-11al>

我们在我的公司里使用了大量的[可翻译的](https://ansible.com)剧本，我们使用[团队城市](https://www.jetbrains.com/teamcity/)作为我们的主要 CI 服务。

在 TeamCity 上，我们的剧本输出如下所示:

[![Ansible Playbook output without the plugin](img/9013069efbcf210f6915d018ba24a30c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tDyOkLA2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/dodevops/ansible-teamcity-callback/raw/master/log_view_without.png)

🤨🙁😞

我不知道你，但我很不喜欢 Ansible playbook 的默认输出。它非常拥挤和庞大，很难检查 Ansible 目前在哪里工作。

TeamCity 有一个非常好的特性叫做[构建脚本交互](https://confluence.jetbrains.com/display/TCD18/Build+Script+Interaction+with+TeamCity)，在这里你可以根据特定的标准格式化你的标准输出，这样它就可以被 TeamCity 解释。

这样你可以打开和关闭程序块，设置变量，标记错误等等。

我说了“打开和关闭块”吗？是的，我做到了，这将成为可翻译剧本的完美特征，对吗？

幸运的是，Ansible 可以被插件扩展，Ansible 的输出由所谓的[回调插件](https://docs.ansible.com/ansible/latest/plugins/callback.html)处理。回调插件对某些事件作出反应(查看[回调库](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/callback/__init__.py)的类定义，获取可覆盖的方法列表)，其中一些事件与游戏或任务的开始有关。

所以我扩展了原来的默认输出插件，覆盖了游戏和任务的特定方法。

有了这个插件，日志现在看起来像这样:

[![Ansible Playbook output with the plugin](img/f739a94d534b2358e159f4f77388ad2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6AgoBIT---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/dodevops/ansible-teamcity-callback/raw/master/log_view_with.png)

😍😍😍

所有结构良好的团队甚至可以计算游戏和任务的时间！

在 GitHub 上查看:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [渡渡鸟](https://github.com/dodevops)/[ansi ble-team city-callback](https://github.com/dodevops/ansible-teamcity-callback)

### 一个可响应的回调插件，用于输出适合团队城市的内容

<article class="markdown-body entry-content container-lg" itemprop="text">

# Teamcity 的 Ansible 回调插件

## 介绍

这个 [Ansible](https://ansible.com) [回调插件](https://raw.githubusercontent.com/dodevops/ansible-teamcity-callback/master/)格式化 Ansible 剧本的输出，以便 TeamCity 能够更好地解释它。

## 为什么？

因为 Teamcity 中的 Ansible 日志看起来像这样:

[![Log without this plugin](img/c53d116ffd1b026e5d8c8add0e4a21f9.png)](https://raw.githubusercontent.com/dodevops/ansible-teamcity-callback/master/log_view_without.png)

有了这个插件，它们看起来像这样:

[![Log with this plugin](img/575c6bbd0489d914b84d553a487e579d.png)](https://raw.githubusercontent.com/dodevops/ansible-teamcity-callback/master/log_view_with.png)

所有的游戏和任务都被很好地放入它们自己的模块中，这样 TeamCity 就可以折叠它们并计算不同的时间。

## 使用

你有不同的选择来使用这个插件:

*   在您的剧本库中
    *   直接在您的剧本所在的位置创建一个名为`callback_plugins`的文件夹
    *   下载[文件 teamcity.py](https://raw.githubusercontent.com/dodevops/ansible-teamcity-callback/master/teamcity.py) 并将其放在`callback_plugins`目录中
*   别的地方
    *   [下载](https://github.com/dodevops/ansible-teamcity-callback/archive/master.zip)或[克隆](https://github.com/dodevops/ansible-teamcity-callback.git)插件，将其解压缩，并将文件夹放在可访问的地方(即使用[团队城市工具功能](https://confluence.jetbrains.com/display/TCD18/Installing+Agent+Tools)将插件分发给所有代理)
    *   使用下面的环境变量告诉 Ansible 在哪里可以找到你的插件:`export ANSIBLE_CALLBACK_PLUGINS=<path of your plugin>`

设置以下环境…

</article>

[View on GitHub](https://github.com/dodevops/ansible-teamcity-callback)