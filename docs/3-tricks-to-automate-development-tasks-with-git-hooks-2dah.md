# 使用 Git 挂钩自动化开发任务的 3 个技巧

> 原文：<https://dev.to/shameemreza/3-tricks-to-automate-development-tasks-with-git-hooks-2dah>

Git 版本控制系统，像许多其他系统一样，附带了一些技巧，使得它可以通过编程进行扩展。在本文中，我们将学习其中的一个，钩子，它允许自动操作以及许多典型的 Git 命令。

钩子是在一些重要的 Git 事件之前或之后运行的程序。例如，在完成 git 提交之前，在更改分支之后或者在 git 推送期间发送更改之前。

它们存储在。git / hooks /每个克隆项目的目录，它们必须是可执行脚本或程序(在类 Unix 系统上，它们必须具有执行权限)。

要向您的项目添加一个挂钩，只需用“事件”的名称(例如，pre-commit，Git 文档中提供了所有可能的名称)将您想要自动运行的程序复制或链接到该目录。

如果程序是一个脚本，并且您希望其他协作者能够使用它，那么将它包含在项目的根目录中并在中创建对它的直接访问是很方便的。git / hooks /:

```
ln -s ../../pre-commit.sh .git/hooks/pre-commit 
```

让我们来看看 Git 钩子的一些实际应用。

## 1。在提交之前检查代码的质量

在确认提交之前执行的钩子是有用的，因为它们允许我们在库的版本树中反映那些变化之前对添加或删除的代码进行检查。

如果挂钩失败或返回错误代码，将不会提交。

在下面的示例中(对 Unix 系统有效，如 Linux 或 macOS)，我们使用 Node.js 中的 jslint 包在完成提交之前检查 JavaScript 应用程序代码的质量:

```
#!/bin/bash
# pre-commit.sh 
# Save unconfirmed changes STASH_NAME="pre-commit-$(date +%s)"
git stash save -q --keep-index $STASH_NAME

# Checks and tests jslint my_application.js || exit 1

# Recover saved changes STASHES=$(git stash list)
if [[ $STASHES == "$STASH_NAME" ]]; then
  git stash pop -q
fi 
```

您可以使用相同的策略对应用程序启动一组测试或其他必要的检查，例如，搜索密钥或秘密令牌以避免将它们输入到存储库中。

## 2。上传变更时生成文档

如果在我们的项目中，我们有一个来自代码的文档生成器，我们可以使用预推类型的钩子定期执行它。

我们只需启动必要的命令来组合某个目录(例如 docs /)中的所有文档，并将其添加到新的提交中。

在下面的列表中，我向您展示了几个可能用于此目的的命令示例:

```
#!/bin/bash
# pre-push.sh 
# Generate the documentation doxygen Doxyfile

# Another example, with Python: pydoc3 -w docs/

# Another example, with R: Rscript -e 'pkgdown::build_site()'

# Add and confirm the changes related to the documentation git add docs/
git commit -m "Update documentation ($(date +%F@%R))" 
```

这样做的好处是，如果您使用 GitHub Pages 或类似的服务来提供您的在线文档，您将始终能够更新您的代码，并且它不会过时。

## 3。更改分支时检查依赖关系

最后，钩子的一个非常有趣的应用是在项目中改变分支时更新已安装的依赖项。

如果您使用开发语言的包管理器和依赖项(Python 中的 Pip，Node.js 中的 npm，Nuget in。红宝石色的网、捆扎机、铁锈色的货物等。)，下面这个例子会很有用。

下面的代码清单对应于一个后签出挂钩，它所做的是检查前一个分支和新分支之间的依赖文件是否已经改变(在本例中，是 Ruby 的 Gem 文件)，在这种情况下，它方便地执行安装程序(在本例中，是 bundle)。

```
#!/bin/bash
# post-checkout.sh 
if [ $1 = 0000000000000000000000000000000000000000 ]; then
  # If we are in a recently cloned project, compare with the empty directory
  old=$(git hash-object -t tree /dev/null)
else
  # The first argument is the hash of the previous HEAD
  old=$1
fi
if [ -f Gemfile ] &&
  git diff --name-only $old $2 | egrep -q '^Gemfile|\.gemspec$'
then
  # Empty $ GIT DIR prevents problems if bundle calls git
  (unset GIT_DIR; exec bundle)
  # Checkout is completed even if the bundler fails
  true
fi 
```

您可以通过更改所使用的依赖项文件(例如 package.json 或 requirements.txt)的 Gemfile 和相应命令的 bundle(NPM install 或 pip install -r requirements.txt)来修改此代码，供您自己使用。