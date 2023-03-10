# 如何用 PhpStorm 和 Homestead 设置调试

> 原文：<https://dev.to/daniel_werner/how-to-set-up-debugging-with-phpstorm-and-homestead-484g>

许多 php 开发人员在调试代码时依赖于 var_dump(或者它在框架中的化身:debug()、dump()、dd()等)，在大多数情况下这很好，但并不总是足够好。当试图解决复杂的问题或修复顽固的 bug 时，PhpStorm 强大的调试功能就派上了用场。调试代码比到处放转储更快更有效。

让我们来看看什么是设置调试的短文:

*   PhpStorm
*   家园(或任何其他流浪虚拟机)
*   Xdebug 扩展

在代码示例中，请根据您的 php 版本更改路径，我使用的是 php 7.2。

## 设置 Xdebug

如果你没有安装 xdebug，请在这里找到安装说明:[https://xdebug.org/docs/install](https://xdebug.org/docs/install)

用你喜欢的编辑器打开 Xdebug 配置，在这个例子中我将使用 nano:

```
sudo nano /etc/php/7.2/fpm/conf.d/20-xdebug.ini 
```

在 xdebug 配置中添加/更改以下设置

```
zend_extension=xdebug.so
xdebug.remote_enable = 1
xdebug.remote_connect_back = 1
xdebug.remote_port = 9000
xdebug.max_nesting_level = 512 
```

保存文件，重启 php fpm:

```
sudo service php7.2-fpm restart 
```

如果一切都设置正确，您应该在 phpinfo()的输出中看到安装并启用了 Xdebug 扩展:

[![](img/81f63c030ff4c2bf716d2c43297970a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dtOjP90_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://42coders.com/wp-content/uploads/2019/05/pasted-image-0-1024x448.png)

## 设置 PhpStorm

在菜单中选择运行/编辑配置，并添加新的 PHP 网页配置:

[![](img/1b5c60c1286c8c709d141e1eeb93d8ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AwsqyM-O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://42coders.com/wp-content/uploads/2019/05/unnamed-5-1024x647.png)

向配置中添加一个名称，填写起始 url，并从下拉选项中选择浏览器和服务器:

[![](img/4fbdfefc6b1cb43daa941ca9c16e6089.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eeI9pYyg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://42coders.com/wp-content/uploads/2019/05/unnamed-4-1024x649.png)

如果服务器下拉列表为空，您需要添加一个新的服务器。如果您使用默认的漫游配置，服务器的 ip 地址是:192.168.10.10。选中使用路径映射复选框，并在 homestead 中设置项目的绝对路径，Homestead 中的默认位置是/home/vagger/code/project(我用的是 www 而不是 code)

[![](img/9652e14c6783fa9bf8bd79a77393ef0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JTHsgsf_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://42coders.com/wp-content/uploads/2019/05/unnamed-2.png)

就是这样！单击右上角的调试图标，浏览器将打开项目的 url，调试会话将开始。您可以通过单击代码左侧行号旁边的来放置断点:

[![](img/8e05570ddeba2a660f7fbd4ffe7027d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IHx9-MFI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://42coders.com/wp-content/uploads/2019/05/unnamed-3-1024x168.png)

调试愉快！

[如何用 PhpStorm 和 Homestead](https://42coders.com/how-to-set-up-debugging-with-phpstorm-and-homestead/) 设置调试的帖子最早出现在 [42 Coders](https://42coders.com) 上。