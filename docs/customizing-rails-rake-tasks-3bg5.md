# 自定义 Rails rake 任务

> 原文：<https://dev.to/vinistock/customizing-rails-rake-tasks-3bg5>

# 动机

Rails rake 任务是自动化开发人员或其他机制(例如:调用 rake 任务的部署)使用的特定操作的命令。许多任务都配置了现成的 Rails 来提供重要的功能。在这篇文章中，我将解释如何覆盖任务并改变它们的步骤来为你自己的应用程序定制它们。

这里有一些现成任务的例子:

```
$ rake -T      # Lists all rake tasks for the application
$ rake         # Default rake task. On a fresh application, does the same as rake test
$ rake test    # Task to run your tests (replaced by "rake spec" if using rspec instead of minitest)
$ rake db:seed # Populates the database using the seeds.rb file 
```

Enter fullscreen mode Exit fullscreen mode

然而，任务很容易被覆盖以执行不同的动作。这对于为构建过程定义步骤很有用，构建过程可能不仅仅包括运行测试套件，或者仅仅是为了改变现有任务的行为。

定制 rake 任务以满足您的需求可以提高您的生产力，并有助于保证项目中的每个人都使用标准化的过程(或者至少他们有工具可以轻松地做到这一点)。

# 怎么做？

覆盖 rake 任务非常简单。它涉及两件事:清除原始任务和重新定义它。任何代码都可以放入新定义中，但是为了简单起见，我建议用它们自己的 rake 任务来整齐地组织每个步骤。

### 如何创建新的耙子任务

用户定义的 rake 任务位于 lib/tasks 文件夹中。任何以“.”结尾的文件。耙子”将自动被轨道拾起并装载。

```
# lib/tasks/my_task.rake

namespace :custom do
  desc "This take does something useful!"

  task :do_it do
    puts "Do something useful!"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。现在，您可以使用下面的命令调用新的 rake 任务。在下一节中，我们将介绍如何从其他任务中调用它。

```
$ rake custom:do_it 
```

Enter fullscreen mode Exit fullscreen mode

### 如何覆盖已有的任务

要覆盖任务，清除当前行为并在 Rakefile 中重新定义。下面的例子将覆盖默认的 rake 来调用我们的自定义任务。

```
# Rakefile
# frozen_string_literal: true

require_relative "config/application"

Rails.application.load_tasks
Rake::Task["default"].clear

task :default do
  Rake::Task["custom:do_it"].invoke
end 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了！默认的 rake 任务可以被配置为执行您的应用程序需要的任何任务。

在下一节中，我将介绍 Rails 应用程序的常规设置。

# 我的首选配置

我喜欢使用的配置覆盖了默认和测试 rake 任务，将它们与一些工具集成在一起。每个步骤如下所述(检查各自报告中的单个工具设置和配置)。

如果这些步骤中的任何一个中断，构建过程就会停止并失败。

**默认**

步骤按以下顺序运行

*   [制动员](https://github.com/presidentbeef/brakeman) -安全代码分析
*   [并行测试](https://github.com/grosser/parallel_tests) -并行运行测试
*   [Rubocop](https://github.com/rubocop-hq/rubocop) -代码质量分析
*   Rails 最佳实践-Rails 分析的最佳实践
*   [臭气](https://github.com/troessner/reek) -代码气味分析

**测试**

这一个简单地代替运行测试来使用并行

*   [并行测试](https://github.com/grosser/parallel_tests) -并行运行测试

现在我们已经完成了高级描述，让我们进入正题。这里是 Rakefile，包含被覆盖的任务和单个任务定义本身。

```
# Rakefile
# frozen_string_literal: true

require_relative "config/application"
require "rubocop/rake_task"

Rails.application.load_tasks
Rake::Task["default"].clear
Rake::Task["test"].clear

task :default do
  Rake::Task["brakeman:check"].invoke
  Rake::Task["parallel:test"].invoke

  RuboCop::RakeTask.new(:rubocop)
  Rake::Task["rubocop"].invoke
  Rake::Task["rails_best_practices:run"].invoke
  Rake::Task["reek:run"].invoke
end

task :test do
  Rake::Task["parallel:test"].invoke
end 
```

Enter fullscreen mode Exit fullscreen mode

刹车手耙子任务

```
# lib/tasks/brakeman.rake
# frozen_string_literal: true

namespace :brakeman do
  desc "Check your code with Brakeman"

  task :check do
    require "brakeman"
    result = Brakeman.run app_path: ".", print_report: true, pager: nil
    exit Brakeman::Warnings_Found_Exit_Code unless result.filtered_warnings.empty?
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

rails 最佳实践 rake 任务

```
# lib/tasks/rails_best_practices.rake
# frozen_string_literal: true

namespace :rails_best_practices do
  desc "Run rails best practices"

  task :run do
    puts "Running rails best practices!"
    puts `rails_best_practices`
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

臭耙任务

```
# lib/tasks/reek.rake
# frozen_string_literal: true

namespace :reek do
  desc "Run reek"

  task :run do
    puts "Running reek!"
    bundle exec "reek ."
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

gem 文件

```
# Gemfile
group :development, :test do
  gem "brakeman", require: false
  gem "parallel_tests"
  gem "rails_best_practices", require: false
  gem "reek", require: false
  gem "rubocop", require: false
end 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

被覆盖的 rake 任务可以提高生产率，并有助于在团队成员中实施良好的实践。好好利用他们！

你会将哪些其他工具与 rake 任务集成在一起？

这种能力还有什么其他用途？