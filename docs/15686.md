# 测试驱动开发入门

> 原文：<https://dev.to/chasestevens/getting-started-with-test-driven-development-1o8f>

测试驱动开发被认为是软件行业的最佳实践，然而许多团队发布代码时没有测试。

## 这是什么

测试驱动开发，或称 TDD，是一种通过在编写代码之前编写测试来创建软件的方法。

鲍勃大叔用三条规则描述了 TDD:

*   除非是为了通过失败的单元测试，否则不允许编写任何产品代码。
*   不允许编写任何超过足以导致失败的单元测试；编译失败就是失败。
*   除了足以通过一个失败的单元测试之外，您不允许编写更多的产品代码。

为尚不存在的代码编写测试似乎不太直观；然而，通过首先编写测试，我们确保我们对我们的代码将做什么有一个明确的预期，并且我们也保证我们不会拖延和表写测试，直到为时已晚。

尽管记下想到的任何代码并一起编写代码直到编译器运行似乎更容易，但你最终会花更多的时间来调试你匆忙编写的代码，而不是编写测试，这将导致不合标准的代码，更糟糕的是，你没有为未来编写任何测试。

对于小型项目或编码练习来说，测试可能不是必需的，但是为了在可预见的将来保持您的程序和项目的健康和稳定，测试驱动开发是一条必经之路。

## 为什么重要

有些人对待测试写作如此认真，以至于他们采纳了“如果不测试，它就坏了”的口头禅。

测试驱动开发对调试有很大的帮助，并且减少了寻找 bug 所花费的时间。

首先编写测试的一个好处是它们不会在混乱中丢失。假设你的截止日期很紧，为了节省时间，你跳过了编写测试。你发布了新特性，然后你又回到了工作岗位，在同样的期限内，不得不从你的开发中削减测试。很快，您就发布了一个包含错误代码且没有测试的完整代码库！

通过使用测试驱动开发并首先编写测试，

## 如何实现

许多编程语言都有用于测试的库和框架。JavaScript 有 [Jest](https://jestjs.io/) 和 [Mocha](https://mochajs.org/) 。红宝石有 [rspec](http://rspec.info/) 。Python 内置了测试模块和 Pytest([https://docs.pytest.org/en/latest/](https://docs.pytest.org/en/latest/))。Java 有 JUnit([https://junit.org/junit5/](https://junit.org/junit5/))——如果它是一种编程语言，它可能有一个测试框架。

无论您选择什么类型的框架，您都将编写一个具有预期结果的测试。如果您的预期结果或断言评估为真，那么测试通过。如果为假，则认为测试失败。

让我们看一下在 Ruby on Rails 中构建的 url 缩短 web 应用程序中创建 url 对象的两个测试。

```
require 'test_helper'

class UrlsControllerTest < ActionDispatch::IntegrationTest
  test "should create a new url" do
    url = Url.new
    assert url.save
  end

  test "should only save a unique url" do
    url = Url.new
    url.text = "https://www.amazon.com/Ruby-Under-Microscope-Illustrated-Internals/dp/1593275277"
    assert_not url.save, "Saved a duplicated URL"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有两个测试，测试“应该创建一个新的 url”和测试“应该检索一个现有的 url”

第一个测试创建了一个新的 url 对象，然后断言我们可以将 url 保存到数据库中。Ruby 评估 url 对象上的 save 方法是否成功完成，并返回 true，这表示断言成功。

第二个测试确保我们只保存唯一的 url——如果 url 已经存在于我们的数据库中，我们宁愿让用户参考现有的 URL 对象。然而，我们不是检查实际数据库中的数据，而是在所谓的测试夹具中创建虚拟数据。

测试夹具的目的是确保我们有一个运行测试的标准环境，以便结果是可重复的。

这里我们在 urls.yml 文件中有一个对象，我们可以用它来测试我们的代码。

```
ruby_book_page:
  text: https://www.amazon.com/Ruby-Under-Microscope-Illustrated-Internals/dp/1593275277
  short: f0r 
```

Enter fullscreen mode Exit fullscreen mode

因为这个对象在 urls.yml 文件中，所以我们的代码将它视为数据库中现有的 url 对象。我们的代码创建一个新的 url，将 Amazon 链接分配给 text 属性，当保存不成功并返回 false 时，我们的 assert_not 测试就成功了。

没有任何验证，url 将被保存，我们的测试将失败。然后，我们将代码添加到 url 模型中，该模型将验证每个 url，并且只接受唯一的 URL。

```
class Url < ApplicationRecord
  validates :text, uniqueness: true
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们再次运行我们的测试，并看到它通过了，从而完成了测试驱动开发的过程(至少对于那行代码来说)。

有大量不同的方法和途径来测试你的代码——希望这能帮助你朝着正确的方向迈出一步。