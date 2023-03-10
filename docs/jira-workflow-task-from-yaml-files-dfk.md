# YAML 文件中的 JIRA 工作流任务

> 原文：<https://dev.to/diegonalvarez/jira-workflow-task-from-yaml-files-dfk>

使用案例:

在我们公司，我们有不同的领域，跨越 JIRA 的工作任务和汇合。

有时我们需要为一个共同的目标设定 20 个左右的任务。例如，这些任务是从我们的客户那里获得并运行一些定制。

在我将要使用的示例中，我们需要:

1.  [1]史诗任务
2.  [4-5]不同领域的主要任务
3.  [4-5]每个主任务的子任务
4.  将任务与标签相关联
5.  把他们之间的任务联系起来
6.  附加链接(Zendesk、Confluence、docs 等)

这个动作在这时是由一个人执行的，但是有时你忘记做一个任务，或者可能是创建任务之间的关系(块，关系)。这样我们可以避免人为错误，我们设置结构，然后脚本自动生成所有的任务。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [迪戈纳瓦雷斯](https://github.com/diegonalvarez) / [吉拉-工作流-任务](https://github.com/diegonalvarez/jira-workflow-tasks)

### 吉拉工作流任务

<article class="markdown-body entry-content container-lg" itemprop="text">

# JIRA 工作流任务

此项目允许您创建一个工作流模板，以在吉拉建立任务。您只需要在多个 YML 文件中设置规范，然后脚本将根据 YML 配置生成任务及其关系。

## 要求

该项目使用两个库:

*   [PHP 吉拉 Rest 客户端](https://github.com/lesstif/php-jira-rest-client)
*   [雅姆交响曲](https://symfony.com/doc/current/components/yaml.html)

此库已经加载了 composer

## 入门指南

要开始，有必要设置一些环境变量。您需要将`.env.example`文件复制为。环境文件。

在这里设置 JIRA 属性:

```
JIRA_HOST="https://your-jira.host.com"
JIRA_USER="jira-username"
JIRA_PASS="jira-password-OR-api-token" 
```

### 安装

首先安装 composer 来安装库。

```
composer install 
```

现在有必要设置模板

### 模板

该项目已经成为一个示例模板。模板存储在模板文件夹中。

*模板文件夹*

要让一个项目工作，你可以复制并粘贴`templates/example`的结构，或者创建你自己的，首先在里面创建一个文件夹…

</article>

[View on GitHub](https://github.com/diegonalvarez/jira-workflow-tasks)

因此，我们可以在项目文件夹中设置许多模板，并运行自动化的工作流程。