# 最小化规格脆性

> 原文：<https://dev.to/epigene/minimising-spec-fragility-26a1>

随着应用程序的增长，规范的数量也在增长，在某种程度上，检查这些测试的健康状况可能是有用的。

不健康的测试可能以多种形式出现——它可能很慢，可能很脆弱(间歇性失败)，可能很难维护，最糟糕的是，测试可能无法测试它应该测试的东西。

这里我将讨论一些应对测试脆弱性的策略。我将展示的例子和工具是针对使用 RSpec 的 Ruby on Rails 应用程序的，但是这些原则应该适用于任何其他环境。

### 测试脆弱性的来源

假设所使用的应用程序代码和编程语言是稳定的，并且不是失败的原因，那么脆弱性只有一个来源——测试环境的变化。这些变化会以几种方式产生。环境中的一些元素被隐含地使用，并且总是在变化，除非被明确地告知不要变化，比如时间。其他元素可能会发生意想不到的变化，比如对 web 请求的响应。还有一些事情会随着应用程序代码的变化而变化，比如数据库内容。意识到这些因素是(web)开发的一个重要部分。努力减轻变化的环境的不利影响的策略包括编写与环境隔离的测试，以及控制或完全防止不必要的变化。

### 控制测试环境

现代(web)应用程序与无数其他进程交互，因此控制所有这些进程可能是徒劳的。然而，幸运的是，最常见的伙伴系统可以很容易地被识别，并且通常有工具在测试中控制与它们的联系。

流行的合作伙伴系统的非详尽列表:

1.  系统时间
2.  系统工具(ImageMagick、wkhtmltopdf 等。)
3.  文件系统
4.  数据库
5.  Web 请求

#### 控制时间

每当被测试的应用程序代码处理时间戳、日期、周期等时，控制测试时间可能是一个好主意。使用 Timecop gem 来冻结这些测试的时间。这里有一个有用的片段

```
RSpec.configure do |config|
  config.before do |test|
    if test.metadata.key?(:freeze_at)
      Timecop.freeze(test.metadata[:freeze_at])
    end
  end

  config.after do
    # return after each test just to be sure
    Timecop.return
  end
end

# then in tests
it "returns correct timestamp", freeze_at: "2018-12-21" do
  # example
end 
```

Enter fullscreen mode Exit fullscreen mode

如果有理由相信套件中可能存在时间脆弱的测试，那么通过模拟不利条件来调查这一点可能是有用的。使用 timecop 旅行到(而不是冻结)一天、一月、一年的变化时刻，就像新年一样。
将时间缩放 100 倍，这样 10ms 就是 1s。
这个想法是重现跨越日、月、年变化的执行，并暴露脆弱的测试。

```
before do
  Timecop.travel("9000-01-01 00:00:00".to_datetime.ago(1.second))
  Timecop.scale(100)
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 控制系统工具调用

就像其他外部依赖一样，系统工具调用应该被清除，最好是全局清除。通常有一些库包装系统工具调用，所以这些库的 api 应该被清除。如果有像`%x'git status'`这样以原始形式被调用的系统工具，那么这个调用应该被包装在一个方法中，而这个方法应该被存根化。

#### 控制文件系统

处理文件时，通常应该使用 tmpfiles。然而，有时应用程序代码确实会改变文件系统的状态，比如创建新文件并写入其中。可以通过三种方式隔离这些变化:

1.  剔除那些所谓的核心语言行为的创建、编写和测试调用
2.  使用一个测试工具，比如一个内存文件系统(fakefs gem ),它可以在两个例子之间进行自我清理。
3.  显式删除已创建的文件，并恢复和`after`挂钩中的更改(容易出错！)

#### 控制数据库

一个充满悲伤和困惑的话题。

基本思想是每个例子应该从一个最大限度空的数据库开始，在这个例子中创建的记录应该在这个例子之后被删除，使数据库回到原始状态。
在实践中，允许写然后执行删除是缓慢且不必要的，因为像 Postgres 和 MySQL 这样的流行数据库支持事务的思想——只在内存中存储诸如记录创建之类的更改，并允许快速恢复。
RSpec 的普通设置使用这些[【交易示例】](https://relishapp.com/rspec/rspec-rails/docs/model-specs/transactional-examples)，这对大多数测试设置来说应该足够了。

不幸的是，旧 Rails 上的一些应用遇到了 capybara 功能测试的问题，即 capybara 进程无法访问在设置事务中创建的记录，开发人员被迫禁用事务示例，允许实际写入数据库，然后必须使用类似于 [database_cleaner](https://github.com/DatabaseCleaner/database_cleaner) 的 gem 进行清理，这往往比事务示例慢得多。
幸运的是，最近的 Rails 版本(4.2+？)本机解决了这个问题，对于旧版本，还有[ActiveRecordSharedConnection](https://test-prof.evilmartians.io/#/active_record_shared_connection.md)backport。

有趣的是，数据库中没有记录的假设会导致具有以下特征的脆弱测试:

1.  硬核 id，确切的实例 id 引用是正确的
2.  相对预期正确时的绝对变化预期
3.  在正确的记录存在和不正确的记录不存在将是正确的范围内的准确记录预期

为了识别在这方面脆弱的测试，尝试在`before(:suite)`期间创建一个常用的模型记录。这将改变 id 和金额，打破不正确的预期。

#### 控制 web 请求

由于 Ruby 有许多处理 web 请求的 gem(rest client ftw)，仅仅淘汰一个库可能是不够的。
幸运的是， [Webmock](https://github.com/bblimke/webmock) 在更深的层次上对 web 请求进行存根化处理，可以在测试期间完全限制真正的外部 web 请求。使用此功能！
在真实请求被拒绝的情况下，如果在测试过程中出现意外请求，Webmock 将会给出一个错误，甚至建议如何处理它。

为了识别与 web 请求相关的脆弱性，尝试告诉 Webmock 对所有请求返回代码 500 响应。

```
require "webmock/rspec"
WebMock.disable_net_connect!(allow_localhost: true)

RSpec.configure do |config|
  before do
    stub_request(:any, %r'.*').to_return(
      body: "everything went wrong", status: 500
    )
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

通过了解脆弱性的常见来源和处理它们的常见策略，开发人员应该能够维护可靠工作的测试套件。

感谢您的阅读。