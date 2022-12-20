# Rubocop 的自定义 binstub 只在更改的文件上运行

> 原文：<https://dev.to/gadimbaylisahil/custom-binstub-for-rubocop-to-run-only-on-changed-files-fcg>

向发展、向社区致敬！几周前，我在`Dev.to`第一次发帖，激励自己发表关于 Ruby、Rails、Javascript 和一般开发的文章。

今天的短文将是只在更改过的文件上运行 Rubocop 并创建 binstubs。

当开始一个项目时，建立和遵循风格指南是相当容易的。然而，当我们继承一个遗留的代码库或者试图将它们应用到已经存在的中型到大型的应用程序中时，我们很快就会感到沮丧，因为已经有成百上千的失败需要我们去修复。

更好的方法是在我们继续开发的时候逐步运行 robocop。每次你接触你的代码库的一部分时，Rubocop 将只分析和 lint 给定的被改变的文件。

那么，我们开始吧。

首先，我们必须创建一个定制的`binstub`来运行 Rubocop，而不是默认的`rubocop`命令。然后，我们将在`binstub`中编写逻辑，使用`git`找到更改的文件，并对它们应用`robocop`。(Aneeta 在这里有一篇关于这个[的文章](https://medium.com/devnetwork/running-rubocop-only-on-modified-files-a21aed86e06d))

创建一个`binstub` Bundler 提供了`bundler --binstubs gemname`，但是我们不需要任何样板文件，所以让我们在`bin/`目录中创建一个名为`rubocop`的空文件。

一旦我们创建了文件，我们将不得不编写逻辑来比较当前对主分支(或您选择的分支)的更改，如下所示:

```
#!/usr/bin/env ruby
# frozen_string_literal: true

class RubocopRunner
  COMPARE_TO_BRANCH = 'master'
  def self.check_branch_validity
    current_branch = `git branch | grep \\* | cut -d ' ' -f2`.strip
    if current_branch.empty? || current_branch == COMPARE_TO_BRANCH
      puts "No git found, or you are still on #{COMPARE_TO_BRANCH} branch."
      exit 0
    end
  end

  def self.check_non_committed_changes
    system("git ls-files -m | xargs ls -1 2>/dev/null | grep '\.rb$' | xargs bundle exec rubocop #{ARGV.join(' ')}")
  end

  def self.check_committed_changes
    system("git diff-tree -r --no-commit-id --name-only head origin/master | xargs bundle exec rubocop #{ARGV.join(' ')}")
  end
end

RubocopRunner.check_branch_validity
RubocopRunner.check_non_committed_changes
RubocopRunner.check_committed_changes 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以运行`bundle exec bin/rubocop`并看到它只应用于已更改的文件。不要忘记将您的`.rubocop.yml`配置文件添加到项目的根目录中。

希望它能有所帮助，如果有任何不尽如人意的地方，请告诉我！

回头见！