# 创建 Ruby 本机扩展

> 原文：<https://dev.to/vinistock/creating-ruby-native-extensions-kg1>

# 注

自从写这篇文章以来，`ext`选项已经被添加到 bundler 中，它自动配置一个新的 gem 作为本地扩展。不要遵循本文中过时的步骤，只需使用`ext`选项创建新的 gem。

```
bundle gem my_gem --ext 
```

Enter fullscreen mode Exit fullscreen mode

# Ruby 的本地扩展有哪些

当用 Ruby 编程时，我们的代码被编译成指令，然后由 Ruby 虚拟机(用 C 构建)执行。

Ruby 本地扩展是使用 RubyVM 的内置函数用 C 编写的库。基本上，它是 C 语言编程，有大量的函数和宏来与虚拟机交互。使用纯 Ruby 可以实现的任何事情也可以使用内置指令来实现。

# 它们为什么有用

与纯 Ruby 相比，本机扩展具有显著的性能优势，这使得它们成为重负载处理的绝佳选择。此外，由于可以访问像`malloc`和`free`这样的 C 函数，它们允许定制的内存管理。

各种流行的宝石都是原生扩展。当运行 bundle install 时，任何向控制台输出“构建本机扩展”的 gem 都是其中之一。一些例子有: [nokogiri](https://github.com/sparklemotion/nokogiri) 、 [mysql2](https://github.com/brianmario/mysql2) 和 [yajl-ruby](https://github.com/brianmario/yajl-ruby) 。

# 如何创建原生扩展

让我们从头开始逐步创建一个本地扩展。

1.  [制作宝石](#creating-the-gem)
2.  [Gemspec 配置](#gemspec-configurations)
3.  [添加编译任务](#adding-the-compile-task)
4.  [extconf 文件](#the-extconf-file)
5.  [创建 C 扩展](#creating-the-c-extension)
6.  [需要共享对象](#requiring-the-shared-object)
7.  [测试扩展](#testing-the-extension)

## 创造宝石

第一步是生成宝石。`bundle gem`命令封装了这个任务。在这种情况下，我们的示例宝石称为“超级”。

```
$ bundle gem super 
```

Enter fullscreen mode Exit fullscreen mode

## Gemspec 配置

创建默认文件后，我们需要修改 gemspec 配置来注册扩展，还需要添加 rake 编译器 gem 来在开发中编译它。重要的修改包括:

1.  将“ext”文件夹添加到`spec.files`。ext 文件夹是本地扩展文件所在的位置
2.  将 extconf.rb 文件路径添加到`spec.extensions`。我们稍后将讨论这个文件是什么。现在，只要记住它需要在路径“ext/NAME_OF_EXTENSION/”中
3.  添加 rake 编译器作为开发依赖项

```
# super.gemspec

lib = File.expand_path("../lib", __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
require "super/version"

Gem::Specification.new do |spec|
  ...
  spec.files = Dir["{app,config,db,lib,ext}/**/*",
                   "MIT-LICENSE",
                   "Rakefile",
                   "README.md"]

  spec.extensions << "ext/super/extconf.rb"

  spec.add_development_dependency "rake-compiler"
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

## 添加编译任务

添加 rake 编译器 gem 之后，编译任务需要对应用程序可用。这是通过在我们的`Rakefile`中添加以下内容来实现的。

```
# Rakefile
...
require "rake/extensiontask"

Rake::ExtensionTask.new("super") do |ext|
  ext.lib_dir = "lib/super"
end
... 
```

Enter fullscreen mode Exit fullscreen mode

## extconf 文件

extconf.rb 文件包含生成用于编译扩展的 makefile 的配置。定制这个文件可能相当棘手，包括操作全局变量、检查当前平台和包含外部库。

例如，如果扩展被分割成许多 C 文件，而不是一个单独的文件，就会变得越来越复杂。然而，单个文件的默认配置是直接的。

```
# ext/super/extconf.rb

require "mkmf"

extension_name = "super"
dir_config(extension_name)
create_makefile(extension_name) 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 C 扩展

这无疑是构建本机扩展最具挑战性的部分。学习如何使用 RubyVM 提供的所有函数和宏需要时间，一些问题可能会让你看着代码时一脸困惑。

类型转换就是一个例子。C float 不同于 Ruby float，需要应用适当的宏来处理值。如果输入值是从 Ruby 到 C 的，那么它需要被转换成 C 浮点数。当返回到 Ruby 上下文时，必须将它转换回 Ruby float。为了简单起见，让我们在超级扩展中避免类型转换。

C 扩展的两个强制步骤是:包含 RubyVM 和为扩展创建一个初始化器(名为 Init_NAMEOFEXTENSION)。其他都是创业板的逻辑。

```
# ext/super/super.c
#include <ruby.h>  
void Init_super(void) {} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看一个例子。我们将使用 C 扩展创建下面的类(这里用纯 Ruby 表示)。

```
# lib/super/super.rb

module Super
  class Super
    def initialize
      @var = {}
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

等效的本机扩展应该是:

```
# ext/super/super.c VALUE SuperModule = Qnil;
VALUE SuperClass = Qnil;

void Init_super();
VALUE super_initialize(VALUE self);

void Init_super() {
    SuperModule = rb_define_module("Super");
    SuperClass = rb_define_class_under(SuperModule, "Super", rb_cObject);
    rb_define_method(SuperClass, "initialize", super_initialize, 0);
}

VALUE super_initialize(VALUE self) {
    rb_iv_set(self, "@var", rb_hash_new());
    return self;
} 
```

Enter fullscreen mode Exit fullscreen mode

类 Super 现在用 pure Ruby 中的 initialize 方法定义。下面列出了函数和宏的详细信息。

1.  *值*表示一般值的宏
2.  Ruby 的 nil 定义
3.  *rb_define_module* 定义一个模块
4.  *rb_define_class_under* 在给定模块下创建一个类。参数是模块对象、字符串形式的类名以及它将继承的类(在本例中是 object)
5.  *rb_define_method* 定义初始化方法。参数是定义方法的类对象、字符串形式的方法名、方法实现函数和参数数量
6.  *rb_iv_set* 将实例变量设置为给定值。接受 self 对象、变量名和变量值
7.  *rb_hash_new* 实例化一个新的 hash。就像 Ruby 中的`{}`

了解可用的 RubyVM 函数和宏对于创建扩展是必不可少的，但是它们无疑很难记住。文档和示例在此过程中提供了宝贵的帮助。

1.  [正式文件](https://docs.ruby-lang.org/en/2.4.0/extension_rdoc.html)
2.  [Ruby C API](https://silverhammermba.github.io/emberb/c/)
3.  [Ruby C cheatsheet](https://github.com/andremedeiros/ruby-c-cheat-sheet)

## 要求共享对象

原生扩展已经写好了。我们现在可以交叉手指，编译它，并要求最终的共享对象。开发中的编译是使用我们之前导入的任务完成的。

```
$ rake compile 
```

Enter fullscreen mode Exit fullscreen mode

结果是 lib 文件夹下的共享对象文件 super.so。在 gem 的模块中要求它将使我们所有的定义可用。

```
# lib/super.rb

require "super/version"
require_relative "super.so"

module Super
end 
```

Enter fullscreen mode Exit fullscreen mode

## 测试扩展

我们的扩展已经完成，可以编写测试来验证它。通过要求共享对象文件，C 中定义的所有东西现在都可以作为 Ruby 使用。因此，扩展也像常规的 Ruby 代码一样被测试。

下面是使用 rspec 对 initialize 方法的一个可能的测试。

```
# spec/super/super_spec.rb

require "spec_helper"

describe Super::Super, type: :lib do
  describe ".initialize" do
    subject { described_class.new }

    it "sets var as an empty hash" do
      var = subject.instance_variable_get(:@var)
      expect(var).to eq({})
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

使用纯 Ruby 或 C 原生扩展是一种权衡。尽管有显著的性能优势，但与 Ruby 相比，C 扩展增加了读写代码的复杂性。

承诺使用本机扩展必须是一个有意识的决定，负责的团队必须同意额外的维护工作不会超过性能优势。

尽管如此，对于 Rubyist toolbelt 来说，熟悉本机扩展是另一项有用的技能。