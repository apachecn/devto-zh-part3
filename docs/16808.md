# 提高 Ruby 安全性的一个简单方法

> 原文：<https://dev.to/molly/one-easy-way-to-improve-your-ruby-security-21mc>

Ruby 社区中的许多人听说过 [Rubocop](https://rubocop.readthedocs.io/en/latest/) ，并且可能对它有某种爱恨交加的关系。如果你没有听说过这种经常两极分化的宝石，这里是它直接来自 Rubocop 文档的要点。

> RuboCop 是一个 Ruby 静态代码分析器(又名 linter)和代码格式化程序。开箱即用，它将执行社区 [Ruby 风格指南中概述的许多准则。](https://github.com/rubocop-hq/ruby-style-guide)

然而，Rubocop 不仅仅是为了执行风格指南。您知道可以使用 Rubocop 来实施安全最佳实践吗？！

[![](img/83aa01e0afec1c3d91e6dc5dc810ca8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pfQPHmB2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kfkfmzi3qmfporoa7nsh.gif)

没错。没错！Rubocop 有一套内置的[安全警察](https://rubocop.readthedocs.io/en/latest/cops_security/)，可以帮助你编写更安全的 Ruby 和 Rails 代码。警察是:

*   [安全/评估](https://rubocop.readthedocs.io/en/latest/cops_security/#securityeval) -使用评估代表着严重的安全风险。
*   [安全/JSONLoad](https://rubocop.readthedocs.io/en/latest/cops_security/#securityjsonload)——由于潜在的安全问题，更喜欢使用`JSON.parse`而不是`JSON.load`。更多信息参见 [ruby docs](https://ruby-doc.org/stdlib-2.3.0/libdoc/json/rdoc/JSON.html#method-i-load) 。
*   [安全/编组负载](https://rubocop.readthedocs.io/en/latest/cops_security/#securitymarshalload)——由于潜在的安全问题，避免使用`Marshal.load`或`Marshal.restore`。更多信息参见 [ruby docs](https://ruby-doc.org/core-2.3.3/Marshal.html#module-Marshal-label-Security+considerations) 。
*   [安全/开放](https://rubocop.readthedocs.io/en/latest/cops_security/#securityopen)——使用内核#open 代表着严重的安全风险。
*   [安全/YAML load](https://rubocop.readthedocs.io/en/latest/cops_security/#securityyamlload)——由于潜在的安全问题，更喜欢使用`YAML.safe_load`而不是`YAML.load`。更多信息参见 [ruby docs](https://ruby-doc.org/stdlib-2.3.3/libdoc/yaml/rdoc/YAML.html#module-YAML-label-Security) 。

除了安全警察之外，还有一些其他的警察可以提高代码的安全性。

*   [轨道/输出安全](https://rubocop.readthedocs.io/en/latest/cops_rails/#railsoutputsafety)——使用`html_safe`或`raw`可能会有安全风险。经常使用这些会导致[跨站脚本](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))漏洞。
*   [Style/MutableConstant](https://rubocop.readthedocs.io/en/latest/cops_style/#stylemutableconstant) -不要将可变对象赋给常量。这种做法的安全含义可能不太明显。例如，如果您意外地用用户数据更新了一个常量，然后该常量被另一个用户使用，那么您就突然有了数据泄漏。出于这个原因，最好确保常数总是不可变的。

## 如何只启用安全警察

如果你想使用 Rubocop 只是为了安全警察，而不是那些讨厌的警察😉，下面是您应该如何设置它。首先，你需要[安装宝石](https://rubocop.readthedocs.io/en/latest/installation/)。

```
gem install rubocop 
```

Enter fullscreen mode Exit fullscreen mode

或者如果您使用的是 Gemfile...

```
gem 'rubocop', require: false 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了 gem，您将需要在您的主目录中配置一个`rubocop.yml`文件。要启用安全性集中的 cops，您的 yaml 文件应该如下所示👇

```
AllCops:
  DisabledByDefault: true

Rails/OutputSafety:
  Enabled: true

Security:
  Enabled: true

Style/MutableConstant:
  Enabled: true 
```

Enter fullscreen mode Exit fullscreen mode

然后你要做的就是运行它！

```
$ rubocop 
```

Enter fullscreen mode Exit fullscreen mode

关于基本用法的更多提示，请查看 [rubocop 文档](https://rubocop.readthedocs.io/en/latest/basic_usage/)。

编码快乐！😃