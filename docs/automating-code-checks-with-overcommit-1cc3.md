# 通过超量提交实现代码检查自动化

> 原文：<https://dev.to/jalerson/automating-code-checks-with-overcommit-1cc3>

几个月前，我在寻找一些东西来防止我用调试器调用提交/推送代码，例如`byebug`。然后我发现了一个我此刻不知道的东西: [Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) 。Git 挂钩允许在发生特定操作时运行定制脚本，例如:提交、推送、合并、签出等。

你可以在这些自定义脚本中放入任何你想要的东西，包括检查你是否做了一些*不那么聪明的*，例如，用`byebug`调用推送代码。如果你在 Google 中搜索，你会发现一些 Git 钩子脚本来阻止你这样做。

但是后来，作为一个 Ruby 开发者，我想:*嘿，总有一个宝石，对吧！？*。对！这叫做[过度承诺](https://github.com/brigade/overcommit)。Overcommit 是一个 Ruby gem，它允许您轻松地管理和配置 Git 挂钩。

开始使用超量承诺非常简单:

```
$ gem install overcommit
$ overcommit --install 
```

Enter fullscreen mode Exit fullscreen mode

`overcommit --install`命令将在当前文件夹中创建一个`.overcommit.yml`设置文件，并备份现有的钩子。[默认设置文件](https://github.com/brigade/overcommit/blob/master/config/default.yml)包括以下钩子:

*   **CapitalizedSubject** :确保提交消息主题行以大写字母开头
*   **EmptyMessage** :检查提交消息是否为空
*   **SingleLineSubject** :确保提交消息主题行后有一个空行
*   **TextWidth** :确保主题和提交消息行占用的列数在首选限制之内
*   **尾随句点**:确保提交消息主题行没有尾随句点
*   **AuthorEmail** :检查作者电子邮件地址的格式
*   **AuthorName** :确保提交作者的名字至少包含名和姓
*   **断开符号链接**:检查断开的符号链接
*   **CaseConflicts** :检查在不区分大小写的文件系统中会发生冲突的文件

当然，你可以很容易地禁用这些钩子:

```
 CapitalizedSubject:
    enabled: false 
```

Enter fullscreen mode Exit fullscreen mode

每次在设置文件中执行更改时，还需要更新运行`overcommit --sign`的签名。您也可以通过在您的`.overcommit.yml`文件中设置`verify_signatures: false`来禁用签名检查。然而，在这样做之前，请[阅读安全含义](https://github.com/brigade/overcommit#security)。

# 推荐内置挂钩

本节描述的挂钩是我目前正在使用和推荐的。你也可以在钩子标题的括号中找到钩子在哪个动作中被执行。

## [RuboCop](https://docs.rubocop.org/) (预提交)

对任何修改过的 Ruby 文件运行 RuboCop。

```
 RuboCop:
    enabled: true
    on_warn: fail
    problem_on_unmodified_line: ignore
    command: ['bundle', 'exec', 'rubocop'] 
```

Enter fullscreen mode Exit fullscreen mode

*   `on_warn: fail`:出现警告时阻止提交
*   `problem_on_unmodified_line: ignore`:忽略您没有添加/更改的代码行中的问题

## [联邦审计](https://github.com/rubysec/bundler-audit)(预提交)

检查 Gemfile.lock 中易受攻击的 gems 版本

```
 BundleAudit:
    enabled: true
    flags: ['--update'] 
```

Enter fullscreen mode Exit fullscreen mode

*   `flags: ['--update']`:检查前强制 BundleAudit 更新

## [联邦检查](https://github.com/brigade/overcommit/blob/master/lib/overcommit/hook/pre_commit/bundle_check.rb)(预提交)

除非 git 忽略了`Gemfile.lock`,否则检查本地 Gemfile.lock 是否匹配 Gemfile。

```
 BundleCheck:
    enabled: true 
```

Enter fullscreen mode Exit fullscreen mode

## [禁止分支](https://github.com/brigade/overcommit/blob/master/lib/overcommit/hook/pre_commit/forbidden_branches.rb)(预提交和预推送)

防止提交到与配置的模式之一匹配的分支。

```
 ForbiddenBranches:
    enabled: true
    branch_patterns: ['master'] 
```

Enter fullscreen mode Exit fullscreen mode

## [更快](https://github.com/DamirSvrtan/fasterer)(预提交)

对任何修改过的 Ruby 文件运行`fasterer`。

```
 Fasterer:
    enabled: true
    exclude:
      - 'vendor/**/*.rb'
      - 'db/schema.rb' 
```

Enter fullscreen mode Exit fullscreen mode

## 自定义脚本(预提交)

提交前运行自定义命令/脚本。

```
 CustomScript:
    quiet: true
    enabled: true
    command: ['i18n-tasks', 'normalize'] 
```

Enter fullscreen mode Exit fullscreen mode

前一个钩子将在提交前运行`i18n-tasks normalize`。`normalize`是[的任务 i18n——任务](https://github.com/glebm/i18n-tasks)宝石。

## [BundleInstall](https://github.com/brigade/overcommit/blob/master/lib/overcommit/hook/post_checkout/bundle_install.rb) (后结帐、后合并和后重写)

当在存储库的依赖关系中检测到变化时运行`bundle install`。

```
 BundleInstall:
    enabled: true 
```

Enter fullscreen mode Exit fullscreen mode

# 其他(大概)有用的内置钩子

这些是我目前没有使用的钩子，但是我愿意试一试。

*   美食评论家(food critical):编写更好更安全代码的林挺工具
*   [RailsBestPractices](https://github.com/railsbp/rails_best_practices) :检查 Rails 代码质量的代码度量工具
*   [RailsSchemaUpToDate](https://github.com/brigade/overcommit/blob/master/lib/overcommit/hook/pre_commit/rails_schema_up_to_date.rb) :检查模式文件是否符合迁移
*   [臭气](https://github.com/troessner/reek):红宝石代码气味探测器
*   Brakeman :专为 Rails 应用设计的漏洞扫描器。它静态地分析代码以发现安全问题
*   [FixMe](https://github.com/brigade/overcommit/blob/master/lib/overcommit/hook/pre_commit/fix_me.rb) :检查“令牌”(`TODO`、`FIXME`等)。)提交前的字符串
*   [BundleOutdated](http://bundler.io/bundle_outdated.html) :列出已安装的 gem 以及可用的更新版本

# 找工作？

如果你在找工作，并且住在法兰克福附近，或者你愿意搬家，我们在 [creditshelf](https://creditshelf.com) 有职位空缺。**所有职位面向讲英语的人**。

*   [Ruby on Rails 开发者](https://creditshelf-jobs.personio.de/job/26809)
*   开发工程师
*   [前端开发者](https://creditshelf-jobs.personio.de/job/99614)
*   [软件质量工程师](https://creditshelf-jobs.personio.de/job/99754)
*   [Python 开发者](https://creditshelf-jobs.personio.de/job/79886)
*   [Fullstack Java/Python 开发者](https://creditshelf-jobs.personio.de/job/79886)