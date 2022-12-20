# Rspec 基础知识(1)

> 原文：<https://dev.to/k_penguin_sato/rspec-basics1-4k38>

# 目录

1.  [什么是 Rspec？](#what-is-rspec?)
2.  如何在您的 RoR 应用程序上安装 Rspec？
3.  [基本结构](#basic-structure)
    *   [形容](#describe)
    *   [它](#it)
    *   [期待&来/不来](#expect-to-not_to)
    *   [语境](#context)
    *   [匹配者](#matchers)
    *   [相等匹配器](#equality-matchers)
    *   [比较匹配器](#comparison-matchers)
    *   [谓词匹配器](#predicate-matchers)
    *   [类型匹配器](#type-matchers)
    *   [成为匹配者](#be-matchers)
    *   [包含匹配器](#include-matchers)
    *   [预期误差](#expect-error)
    *   [响应匹配器](#respond_to-matchers)

# 什么是 Rspec？

RSpec 是面向 Ruby 程序员的行为驱动开发工具。是一种结合了测试驱动开发、领域驱动设计和验收测试驱动规划的软件开发方法。

# 如何在您的 RoR 应用程序上安装 Rspec？

您必须将`rspec-rails`添加到 gem 文件中的`:development`和`:test`组中。

```
group :develop, :test do 
   gem 'rspec-rails'
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过运行下面的代码下载并安装它。

```
$  bundle install 
```

Enter fullscreen mode Exit fullscreen mode

要初始化`spec/`目录(规范所在的位置)，您可以通过运行下面的命令来完成。

```
$  rails g rspec:install 
```

Enter fullscreen mode Exit fullscreen mode

这将添加以下用于配置的文件。

*   `.rspec`
*   `spec/spec_helper.rb`
*   `spec/rails_helper.rb`

您可以通过运行`rspec`命令来运行您的规范(执行规范文件)。

```
$  bundle exec rspec 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，上面的命令将运行 spec 目录中的所有`_spec.rb`文件。
通过指定文件路径，您可以只运行这些规范的子集，如下例所示。

```
#  Run only model specs
$  bundle exec rspec spec/models
  #  Run only specs for PostController
$  bundle exec rspec spec/controllers/posts_controller_spec.rb
  #Run only spec on line 8 of PostController
$  bundle exec rspec spec/controllers/posts_controller_spec.rb:8 
```

Enter fullscreen mode Exit fullscreen mode

如果您想使用应用程序中定义的类，不要忘记给每个文件添加`require 'rails_helper'`。

```
require 'rails_helper'

describe 'Post' do
  it 'tests type matchers' do
    ins = Post.create(title: 'title', content: 'content')
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

# 基本结构

为了用真实的例子展示 Rspec 的基本结构，我用`scaffold`命令创建了一个简单的 CRUD RoR 应用程序。您可以通过运行下面的代码来创建相同的应用程序。

```
$  rails g scaffold Post title:string content:text 
```

Enter fullscreen mode Exit fullscreen mode

## 形容

单词`describe`用于定义一个`example group`,这是一组测试的 Rspec 说法。`describe`接受类名和/或字符串参数。您可以像下面的代码一样定义一个`example group`。

```
describe Post do
  #tests
end 
```

Enter fullscreen mode Exit fullscreen mode

## 它

单词`it`是另一个用于定义`example`的 RSpec 关键字。一个例子基本上就是一个测试或者一个测试用例。您可以像下面的代码一样定义一个`example`。

```
describe Post do
  it 'test case' do
    #test content
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 期待&来/不来 _ 来

关键字`expect`用于在 Rspec 中定义一个`expectation`。`to`关键字也被用作`expect`语句的一部分。当你希望期望值为假时，你也可以使用`not_to`关键字。

```
describe 'Post' do
  it 'test case' do
    value = 1
    expect(value).to eq(1)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`eq`关键字是 Rspec [匹配器](https://relishapp.com/rspec/rspec-expectations/docs/built-in-matchers)中的一个。每个匹配器可以与`expect(..).to`或`expect(..).not_to`一起使用，分别定义对一个对象的正面和负面期望。

# 上下文

`context`关键字只是`describe`的一个别名方法，这意味着这两个方法在功能上没有区别。然而，有一个上下文上的差异，通过使用这两种方法可以使您的测试更容易理解。
一般来说，`describe`用于根据被测试的方法或行为来分离测试集，而`context`用于根据条件来分离规格。

```
describe 'Post' do

  context 'name == Jim' do 
    #tests
  end

  context 'name == John' do 
    #tests
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

# 赛者

我们在上面的例子中使用了一个匹配器`eq`。在这里，我将介绍其他类型的匹配器。

## 相等匹配器

Rspec 有几种不同的方法来处理等式。

```
require 'rails_helper'

describe 'Post' do
  it 'tests equality matchers' do
    a = 'value'
    b = 'value'
    expect(a).not_to equal(b) # object identity - a and b refer to the same object
    expect(a).to eql(b) # object equivalence - a and b have the same value
    expect(a).to be == b # object equivalence - a and b have the same value with type conversions
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 比较匹配器

RSpec 提供了许多基于 Ruby 内置操作符的匹配器。这些可用于值的一般比较。我将介绍一些我经常使用的匹配器。关于这个话题的更多信息，请查看[趣味](https://relishapp.com/rspec/rspec-expectations/v/3-8/docs/built-in-matchers/comparison-matchers)。

```
require 'rails_helper'

describe 'Post' do
  it 'tests comparison matchers' do
    a = 1
    b = 2
    expect(a).to be > 0
    expect(a).to be < b
    expect(a).to be >= 0
    expect(a).to be <= b
    expect(a).to be == 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 谓词匹配器

Ruby 对象通常提供像`zero?`、`empty?`和`has_key?`这样的谓词方法。您可以在 Rspec 中使用这些方法，只需在方法前面加上`be_`并去掉问号。

```
require 'rails_helper'

describe 'Post' do
  it 'tests predicate matchers' do
    a = 1
    arry = []
    hash = { key: 'value' }
    expect(a).not_to be_zero
    expect(arry).to be_empty
    expect(hash).to have_key(:key)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 类型匹配器

您可以使用`be_kind_of()`、`be_instance_of()`来测试 Rspec 中对象的类型或类。

```
require 'rails_helper'

describe 'Post' do
  it 'tests type matchers' do
    str = 'string'
    ins = Post.create(title: 'title', content: 'content')
    expect(str).to be_kind_of(String) # same as expect(obj).to be_kind_of(type)
    expect(str).to be_a_kind_of(String) # same as expect(obj).to be_kind_of(type)
    expect(str).to be_a(String) # same as expect(obj).to be_kind_of(type)
    expect(str).to be_an(String) # same as expect(obj).to be_kind_of(type)

    expect(ins).to be_instance_of(Post)
    expect(ins).to be_an_instance_of(Post)  # same as expect(obj).to be_instance_of(type)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 做媒人

有几个相关的`be matchers`。

```
require 'rails_helper'

describe 'Post' do
  it 'tests be matchers' do
    obj1 = true
    obj2 = false
    obj3 = nil
    expect(obj1).to be_truthy
    expect(obj1).to be #It is the same as be_truthy
    expect(obj2).to be_falsey
    expect(obj3).to be_nil
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

# 包含匹配器

您可以使用`include matcher`来指定一个集合包含一个或多个期望的对象。

```
require 'rails_helper'

describe 'Post' do
  it 'tests include matchers' do
    str = 'String'
    arr = [0,1,2]

    expect(str).to include('S')
    expect(str).to include('St', 'r')
    expect(str).not_to include('A')

    expect(arr).to include(0)
    expect(arr).to include(0, 1)
    expect(arr).not_to include(9)
    expect(arr).to include(be_odd.and be < 10)
    expect(arr).to include(a_kind_of(Integer))
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 预期误差

使用`raise_error`匹配器指定代码块引发错误。

```
describe 'Post' do 
  it 'tests errors' do 
    expect { raise "boom" }.to raise_error
    expect { raise "boom" }.to raise_error(RuntimeError)
    expect { raise "boom" }.to raise_error("boom")
    expect { raise "boom" }.to raise_error(/boom/)
    expect { raise "boom" }.to raise_error(RuntimeError, "boom")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 响应 _ 匹配器

使用`respond_to`匹配器指定对象接口的细节。

```
describe 'Post' do
  it 'tests respond_to matchers' do
    str = 'String'
    expect(str).to respond_to(:split)
    expect(str).to respond_to(:to_i, :to_sym)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

更多的匹配者被列在[美味](https://relishapp.com/rspec/rspec-expectations/v/3-8/docs/built-in-matchers)上。
原帖发布在[我的博客](https://k-blog0130.herokuapp.com/posts/9)