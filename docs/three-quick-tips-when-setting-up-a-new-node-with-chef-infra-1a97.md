# 使用 Chef Infra 设置新节点时的三个快速提示！

> 原文：<https://dev.to/sigje/three-quick-tips-when-setting-up-a-new-node-with-chef-infra-1a97>

今晚我在帮助一个人和 Infra 主厨建立一个新的节点。我想记录下来，这样如果其他人遇到了类似的问题，可能会更容易理解发生了什么。

## ⭐ **提示 1** 遇到困难时寻求帮助。

问题陈述是“试图将一个 JSON 文件传递给 chef-zero 以获得额外的属性。”使用的命令是[主厨-客户](https://docs.chef.io/ctl_chef_client.html)。

> 人们可能不知道产品使用的确切术语，所以没有指导很难弄清楚。有专业知识的人可能会用正确的术语给你更多的见解。

为了确保我明白这个人在寻找什么，我问了一些澄清性的问题。这些问题的答案帮助我理解这是建立一个新的实例或者说是[引导](https://docs.chef.io/install_bootstrap.html)。虽然[厨师-客户](https://docs.chef.io/ctl_chef_client.html)页面有有用的信息，但它不像[自举页面](https://docs.chef.io/install_bootstrap.html)那样具体和直接。

## ⭐ **提示 2** 不要把-o 和-j 都传给主厨-客户。

*-o* 覆盖 run_list， *-j* 是你如何指定一个包含你想要运行的配方和属性的文件。因此，我们没有同时使用-j 和-o，而是将-o 后面指定的配置添加到 JSON 文件中。

## ⭐ **提示 3**JSON 文件有一种特殊的格式，看起来不像标准的属性文件。

在节点属性文件中，属性看起来像`node['resolver']['nameservers']`。所以这个:

```
{
    "node['resolver']['nameservers']": ["10.0.0.1"],
    "node['search']": "http://sparkle.corp",
    "run_list": ["recipe[resolver]"]
} 
```

Enter fullscreen mode Exit fullscreen mode

是有效的 JSON，看起来接近节点属性定义中的适当配置。它甚至会被成功接受，但不会有预期的行为。没有错误会指出 converge 上 JSON 文件的问题。

相反，JSON 文件应该是这样的:

```
{
    "resolver": {
        "nameservers": ["10.0.0.1"],
        "search": "http://sparkle.corp"
    },
    "run_list": ["recipe[resolver]"]
} 
```

Enter fullscreen mode Exit fullscreen mode

暂时就这样吧！