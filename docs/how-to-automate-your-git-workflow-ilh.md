# 如何自动化您的 Git 工作流

> 原文：<https://dev.to/rampa2510/how-to-automate-your-git-workflow-ilh>

# 吉特斯

一个基于节点 js 的 cli，可以自动化你的 git 工作流程！！

<center>
![](img/70eb1a2c2000a3edb0c19ef0c7ef33c9.png)
</center>

您必须在本地和远程手动创建 git repo 的日子已经一去不复返了。

现在，您可以使用**“gitesy”**通过 3-4 次点击在本地和远程创建回购

## 安装

在您的命令提示符/终端
中键入以下内容

```
npm i gitesy -g 
```

请注意-将它作为一个全局包安装，否则它可能不会像预期的那样工作

* * *

## 用法

### 创建新的回购

*   要创建本地和远程 repo，请使用带有 repo 名称的 gitesy -n 命令

```
 gitesy -n test 
```

*   之后，如果你是第一次使用它，你会看到一个提示，要求你输入你的凭证，就像这样![](img/513962261d37a4d80bba9364ea351221.png)

输入它们，然后你会得到提示，询问回购的细节，填写它们，然后它会自动创建回购。然后你会得到信息，并开始工作的项目！！！！

*   如果你不是第一次使用，你会看到一个提示，询问 gitesy 是否可以使用你之前输入的信用凭证![](img/c7e5aa832640e07020da24e76023813a.png),创建一个新的回购协议并开始编码是非常容易的。我还添加了 none 选项，只是为了在本地和远程创建一个空的 git repo。

### 添加新模板

*   要添加模板，请创建一个模板，添加功能，然后在同一目录中使用此命令

```
 gitesy -a <nameOfTheTemplate> 
```

[![create template demo](img/702ab6eca08bd32e0e3106b5f33e6049.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A9zl6UP8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f3ob1o27x6igr0p09urs.gif)

* * *

## 后记

你可以在这里为这个项目[做贡献](https://github.com/ram2510/gitesy)

你能做的事情

*   修复错别字
*   添加更多通用模板

请给我你的建议，它们对我很有价值。

点击此处访问国家预防机制网页

如果你喜欢这个 npm 包，请分享它，以便我们可以通过建议使这个包变得更好。谢谢大家！！！！

你可以在 twitter 或 LinkedIn 与我联系。