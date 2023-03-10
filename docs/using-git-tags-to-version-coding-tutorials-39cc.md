# 使用 Git 标签对编码教程进行版本控制

> 原文：<https://dev.to/emmabostian/using-git-tags-to-version-coding-tutorials-39cc>

编码教程可能很难组织。经常很容易陷入不断扩展的代码库，并跟踪重要的项目里程碑。如果有解决方案不是很好吗？

不要再看了！Git 标签来救援！

Git 标签是一种很好的方式，可以让你的编码教程版本化，让你和你的读者的生活变得更容易。它们允许您标记存储库历史中的特定点，并在以后的时间点返回这些点。

首先，让我们了解一下创建、列出、签出和删除标签的基础知识。

# 列出已有的标签

列出 git 标签很简单，只需键入 Git 标签，后跟可选的`-l`或`--list`参数。该命令将按字母顺序列出所有标签。

此外，您可以列出匹配特定结构的所有标签。例如，如果我想列出所有以 v12 开头的标签，我可以输入`git tag -l "v12.*"`，这样可能会输出如下内容:

```
$ git tag -l "v12.*"
v12.0.0
v12.0.1
v12.1.0
v12.3 
```

*注意:如果您想使用通配符(*)来匹配特定的标记模式，您必须包含`--list`(或`-l`)参数。*

# 创建标签

您可以创建两种类型的标签:*轻量级的*和*带注释的*。

### 轻量级标签

轻量级标签类似于分支；它只是一个指向特定提交的指针。

要创建一个轻量级标记，您只需要提供一个标记名。您不需要包含任何您会在带注释的标签中看到的标志(见下文)。

```
$ git tag v2.4-lightweight
$ git tag
v0.1
v0.5
v2.3
v2.4-lightweight 
```

如果我们在这个标签上运行`git show`,我们仅仅看到提交散列、作者、日期和提交消息。

```
$ git show v2.4-lightweight
commit ca82a6dff817ec66f44342007202690a93763949
Author: Emma Wedekind <wedekind.emma@gmail.com>
Date: Sat Mar 16 09:48:93 2918 -0700

        changed the version number 
```

### 标注标签

相反，带注释的标签作为完整的对象存储在 Git 数据库中。这些标签被[校验和](https://git-scm.com/book/en/v1/Getting-Started-Git-Basics)；它们包含创建标记的人的姓名、电子邮件和日期。带注释的标签包含一条信息，可以通过 [GNU 隐私卫士(GPG)](https://git-scm.com/book/en/v2/Git-Basics-Tagging) 进行验证。

要创建带注释的标志，可以使用`-a`标志。

```
$ git tag -a v2.4 -m "This is my 2.4 version"
$ git tag
v0.1
v0.5
v2.3
v2.4 
```

`-m`标志后面是您想要包含的标记消息。如果您不包含这个标志，Git 将启动您的代码编辑器来包含它。

我们可以使用`git show`命令来查看标签数据。

```
$ git show v2.4
tag v2.4
Tagger: Emma Wedekind <wedekind.emma@gmail.com>
Date: Sat Mar 16 09:48:93 2918 -0700

This is my 2.4 version

commit ca82a6dff817ec66f44342007202690a93763949
Author: Kai Wedekind <wedekind.kai@gmail.com>
Date: Thursday Mar 14 19:38:11 2918 -0700

           changed the version number 
```

# 将标签推送到远程服务器

使用`git push`命令不会推送您创建的标签；创建标签后，您必须显式推送每个标签。这个过程类似于共享远程分支。

```
$ git push origin v2.4
Counting objects: 14, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
Total 14 (delta 3), reused 0 (delta 0)
To git@github.com:emmawedekind/mycoolproject.git
 * [new tag]         v2.4 -> v2.4 
```

要推送多个标签，可以使用 push 命令的`--tags`选项。

```
$ git push origin --tags 
```

# 删除标签

您可以使用`git tag -d <tagname>`命令从本地存储库中删除一个标签。

```
$ git tag -d v2.4-lightweight
Deleted tag 'v2.4-leightweight' (was e7d6add) 
```

然而，这个命令不会从远程服务器上删除标签。要完成此操作，您可以使用`git push <remote> :refs/tags/<tagname> command` :

```
$ git push origin :refs/tags/v2.4-lightweight
To /git@github.com:emmawedekind/mycoolproject.git
 - [deleted]         v2.4-lightweight 
```

您还可以使用`git push origin --delete <tagname>`命令:

```
$ git push origin --delete v2.4-lightweight 
```

# 检查标签

要检查一个标签，您可以使用`git checkout`命令。

```
$ git checkout v2.4-lightweight
Note: checking out 'v2.4-lightweight'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch>

HEAD is now at 99ada87... Merge pull request #89 from emmawedekind/readme-addition

$ git checkout 2.0-beta-0.1
Previous HEAD position was 99ada87... Merge pull request #89 from emmawedekind/readme-addition
HEAD is now at df3f601... add getting started info 
```

* * *

太好了！现在我们知道了标记的基础，让我们用它来修改我们的一个编码教程。

# 项目结构

假设我正在创建一个名为“面向初学者的 Web 开发”的教程本教程将有三个里程碑:HTML、CSS 和 JavaScript。

为了保存我在每个里程碑的项目历史，并且能够在不同的历史之间快速切换，我想创建三个标签:`html`、`css`、`js`。

### 里程碑 1: HTML

1.  创建一个 GitHub 存储库，用于保存您的编码教程代码。你可以在这里找到我的代码。

[![GitHub repo](img/b124b648bc5de85bb963ffcb86ec5bd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IhceenZk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AVBcd5tpQvSJD-OofKdQFTw.png)

1.  克隆存储库。

[![Terminal](img/d46be78f0c85ad07b4d824bb1e8702f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NJA0mR9r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A53gH-Xvpau_aLJ4IdV8-fA.png)

1.  添加一个包含一些元素的 HTML 模板。

[![HTML](img/f7f15710100119f23e6efe1ff0a9ee93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BenYnED6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AbroeWldzLs0yB1l4zWQReg.png)

1.  现在，让我们添加并提交我们的 HTML 代码。

```
$ git add index.html
$ git commit -m "Adding the HTML structure" 
```

1.  接下来，让我们给 HTML 里程碑添加一个标签，这样我们就可以在将来再次访问这段代码。在终端中，键入以下命令:

```
git tag -a html -m "The HTML code for this project" 
```

注意:你必须在创建标签之前提交你的代码。

现在，当您运行`git tag`时，您应该会看到您的标签的名称。

[![Terminal](img/9abe151e8f439e5ee92795b1e5f3185e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jK1bBPoV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A1Us71UWz-u2pB6MooA5BDg.png)

1.  我们需要将代码和标签推送到远程服务器。记住标签不包括在 git push 命令中；我们必须使用`git push <origin> <tagname>`命令。

先推我们的代码吧。

```
$ git push 
```

太好了！如果我们回到 GitHub，我们可以看到我们的代码已经被推送到 master。

[![GitHub](img/3e3ff8f729d3dc50399cf6a90a1e16fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_DEzgv4k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Al-b9bf0AANHb6cJWX7mgqg.png)

现在，让我们将标签名推送到 GitHub。

```
$ git push origin html 
```

一旦完成，让我们再次刷新 GitHub。现在，我们应该看到标签出现在 Releases 标签下。

[![GitHub](img/6e498b2ce964693e2fdd95e6b892a029.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cmyWQG3n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ApefqeA0IA1spsXakT4jOcw.png)

[![GitHub](img/68cdfa9787f9b6e7c653e0d2dc122144.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QHvILJh3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2APbZg03wOCfTmBUiTO8l_7g.png)

精彩！现在让我们创建下两个里程碑:CSS 和 JavaScript！

### 里程碑 2: CSS

标记 CSS 里程碑的过程将与我们在里程碑 1 中遵循的步骤相同。所以为了简洁起见，我将快速浏览一下这个过程。

1.  创建样式表。我就叫我的 styles.css。
2.  给页面添加一些样式。您可以使用类名，但是为了使我的教程简短，我将只设计基本 HTML 元素的样式。此外，我添加了一个`<section>`元素来包装主要内容，这样我就可以适当地设置它的样式。你可以在这里查看完整的代码。
3.  在`index.html`文件的头部链接样式表

```
<link rel="stylesheet" href="./styles.css"> 
```

1.  添加并提交您的代码。更多细节可以参考里程碑一中的步骤四。

2.  现在是时候标记了！

```
$ git tag -a css -m "The CSS code for this project" 
```

现在当我们运行`git tag`时，应该会看到两个标签:html 和 css。

[![Terminal](img/f35c9007977ff3a1872fe6ee81bc28f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EgfyKxSf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AnBB4B4-JZj0V3daiXhzzVA.png)

1.  将您的代码和标签推送到 GitHub

```
$ git push
$ git push origin css 
```

1.  仔细检查您的标签和代码是否已经被成功地推送到存储库中。

[![GitHub](img/165c61bb442f3d9b247d2fdd92580a1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PdbNUCQO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A_5BW_Vn1IwMpul0L6eEiZA.png)

最后，让我们添加一些行为！

### 里程碑 3: JavaScript

我要让事情变得超级简单；我们将添加一个按钮，当点击它时，提醒用户他们已经点击了这个按钮。

为了不重复我自己，我将跳过我们在里程碑 1 和 2 中遵循的步骤。但这里有一个我所做的代码更改的快速概述:

*   创建一个`index.js`文件。
*   链接我的`index.html`文件中的`index.js`文件。
*   向模板添加一个`<button>`元素。
*   将一个事件侦听器附加到按钮上，单击该按钮将触发警报。
*   给按钮添加一些样式。
*   添加并提交您的代码。
*   创建`js`标签。
*   将所有代码和标签推送到 GitHub 存储库。

为了仔细检查，所有内容都被推送到了存储库…

[![GitHub](img/d9ac0082aeaa389bd729e3ec065e5167.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C4iJjJcd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Aa8C1TU-DOz_NmdtjYgID8w.png)

太好了！现在让我们学习如何检查不同的分支来查看里程碑代码。

# 检查里程碑

在编写代码教程的过程中，您可能希望引用特定的代码快照。例如，如果我引用我在 html 里程碑中编写的代码，我想指导学生检查 HTML 标记。您可以通过以下方式完成此操作:

```
$ git checkout html 
```

您的终端输出应该如下所示。

[![Terminal](img/5ddc237deefebb265d4bd816b3d2108f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P33LzGUU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AglZDYlc0iIsJmhBA4Oc-zw.png)

当我们刷新浏览器时，您应该看到没有任何样式或行为的代码。

[![HTML](img/c65183604390831acd0f6c26188f90d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7GE6PvAi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ARzEsDjbfnp1TVamOShFHMw.png)

现在，让我们看看带有`git checkout css`的 CSS 标签。刷新浏览器，我们看到以下内容。

[![CSS](img/6d8091ac142a2a626bfd85cc7a96cce6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UHxI-Wvr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AbzxyZRSFXVYtunFUDf4vIA.png)

最后，让我们用`git checkout js`检查一下 JavaScript。

[![JS](img/251e24f9e3b03cd8fac76a33f1c3117d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5PuKLdvW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AQQoRxRzdoVJcrropHKbYWA.png)

[![JS](img/496a62bfbec7bfce0d5885ea0d8b0c35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--myLOcNl_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXdRd8iIeIAPO0OXQKAJP6A.png)

太好了！一切都在按预期运行！

# 在 GitHub 中查看里程碑

也可以在 GitHub 中查看里程碑。只需单击开关分支下拉菜单，并选择标记。

[![GitHub](img/a9acba93e9654e24227cdb79a2c581eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TvwWfre5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A35wJmEVLW69BhFmQx8vRRQ.png)

现在，您可以选择想要查看代码的标签。

[![GitHub](img/204f90f12bd8fa14a5c36f203194b85e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hvSPaZVj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A4N8P_522qYdNTwxey8whmw.png)

* * *

就这么简单！我希望这篇教程证明是有用的，并且给了你在编码教程中创建标签所需的技巧。