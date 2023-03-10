# 使用 TTY::提示您的 CLI 应用程序

> 原文：<https://dev.to/aidiri/using-tty-prompt-for-your-cli-app-1l5a>

我最近成为了熨斗学校(Flatiron School)的一名软件工程学生，这是一所最初位于纽约的编码学校，其分支机构遍布全国。

在我们的第三周，我们负责创建我们的第一个健壮的项目:一个 CLI (Command Lind Interface)应用程序。一个我们必须展示的项目。在我们所有同学面前。太可怕了。但是，严肃地说，CLI 应用程序对初学者来说是一个很好的项目，我强烈推荐它作为加快学习的一个巨大挑战。

对于那些想要将 CLI 应用程序提升到更高水平的人，我向您展示了一个非常棒的宝石，它简单易用，让您的应用程序看起来更上一层楼

# TTY::提示

[TTY::提示符](https://github.com/piotrmurach/tty-prompt#ttyprompt-)，由[彼得·穆拉希](https://github.com/piotrmurach)创建，是一个开源的宝石，它提供了“一个漂亮而强大的交互式命令行提示符”*非常棒。如果您的 CLI 应用程序需要用户输入(这是应该的，为什么不呢？)，这是给你的宝石。它为您提供了很好的选项来显示提示或菜单，您的用户可以填写或从中选择，并使样式整洁美观。像这样:
T5】

## 如何使用

很简单！

1.  将宝石添加到您应用程序的宝石文件:`gem 'tty-prompt'`
2.  用`bundle`执行
3.  或者用`gem install tty-prompt`手动安装在 bash 中

当你在应用程序中使用它时，你需要做的就是创建一个变量`prompt`并创建一个 TTY::提示类的新实例。

```
prompt = TTY::Prompt.new 
```

Enter fullscreen mode Exit fullscreen mode

这样，您可以在这个新的提示变量上调用 gem 提供的任何方法。

```
prompt.ask(‘Your question here') 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## T2】

* * *

## 方法

您最有可能在 CLI 应用程序中使用一些基本的提示方法:

*   [T2`.ask`](https://github.com/piotrmurach/tty-prompt#21-ask)
*   [T2`.yes?`](https://github.com/piotrmurach/tty-prompt#25-yesno)
*   [T2`.mask`](https://github.com/piotrmurach/tty-prompt#24-mask)
*   [T2`.select`](https://github.com/piotrmurach/tty-prompt#262-select)

* * *

#### 问

要问一个用户可以用简单输入来回答的问题，使用`ask`方法。和任何时候你想使用 TTY::Prompt 一样，你首先需要创建一个提示，然后在你创建的提示变量上调用这个方法。

```
prompt = TTY::Prompt.new
prompt.ask('What is your name?') 
# => What is your name? doge
# => "doge" 
```

Enter fullscreen mode Exit fullscreen mode

返回值是用户输入的字符串。假设你的用户输入“doge”，当他们按下 return 键时，不仅你的返回值等于“doge”，而且你的用户可能看起来像这样: [![doge dog](img/40bbf7856c8b06a52ebca7046c7a777d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hrL93f2N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.tynker.com/projecimg/58338362157d72a65c8b456a/doge---doge.png)

* * *

#### 是吗？/没有？

[`yes?`](https://github.com/piotrmurach/tty-prompt#25-yesno) 方法的构建类似，但只有当用户分别按下‘y’或‘n’时，才会返回 true 或 false。

```
prompt.yes?(‘Are you a dog?’)
#=> Are you a dog? yes
#=> true 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用`.suffix`选项，将默认的(Y/n)选项改为(同意/不同意)。

* * *

#### 面具

当你需要用户的机密信息时，比如密码，一个很好的工具是使用 [`mask`](https://github.com/piotrmurach/tty-prompt#24-mask) 方法。

```
prompt.mask(‘Enter your password:')
#=> Enter your password: •••• 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 菜单方式

Prompt 提供了几种不同的方法来创建菜单，用户可以从选项列表中选择一个或多个选项。创建菜单最基本的方法是使用`select`方法。

#### 选择

每当你需要你的用户从选项列表中选择一个时， [`select`](https://github.com/piotrmurach/tty-prompt#262-select) 方法就是你的朋友。您的用户将使用向上/向下键从您提供的选项列表中进行选择，这将返回所选选项的字符串:

```
prompt.select("What's your favorite kind of taco shell?", %w(Soft_taco Hard_taco 
Porque_no_los_dos?)) 
```

Enter fullscreen mode Exit fullscreen mode

```
#What's your favorite kind of taco shell? 
#  Soft_taco
#  Hard_taco
#‣ Porque_no_los_dos?
#=> "Porque_no_los_dos?" 
```

Enter fullscreen mode Exit fullscreen mode

[![porque no los dos girl](img/ae17f25fe4213114e0c0d0a85391c919.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K8a_B6vI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/s4eamxv.jpg%3Ffb)

select 方法还有另外两种变体:

1.  [`multi_select`](https://github.com/piotrmurach/tty-prompt#263-multi_select) 允许用户选择多个选项:

2.  [`enum_select`](https://github.com/piotrmurach/tty-prompt#264-enum_select) 允许用户通过输入选项对应的数字键来选择选项:

```
 prompt.multi_select("What's your favorite pet?", %w(cats dogs fish 
      birds reptiles plants))

      #What's your favorite pet? cats, dogs, plants
      #  ⬢ cats
      #  ⬢ dogs
      #  ⬡ fish
      #  ⬡ birds
      #  ⬡ reptiles
      #‣ ⬢ plants 
```

Enter fullscreen mode Exit fullscreen mode

```
 prompt.enum_select("What's your favorite pet?", %w(cats 
      dogs fish birds reptiles plants))

      #What's your favorite pet? 
      #  1) cats
      #  2) dogs
      #  3) fish
      #  4) birds
      #  5) reptiles
      #  6) plants
      #  Choose 1-6 [1]: 2 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## T2】

* * *

## 炫酷功能

TTY:提示给了你很多个性化提示的方法。让我们看一些更有用的。

* * *

#### 默认

许多方法都有为提示定义默认值的选项。当在异常情况下给出选项时，这是一个很好的特性。或者也许你预见到用户会特别懒惰或者偏执地使用他们的名字；您可以将`default:`值设置为“匿名”。

```
prompt.ask(‘What's your name?', default: 'Anonymous')
# =>
# What is your name? (Anonymous) 
```

Enter fullscreen mode Exit fullscreen mode

TTY::Prompt 足够智能，可以为相当多的方法提供默认选项:

*   **T2`ask`**

```
prompt.ask(‘What industry do you work in?', default: ’Tech')
# =>
# What industry do you work in? (Tech) 
```

Enter fullscreen mode Exit fullscreen mode

*   **T2`yes?`**

```
prompt.yes?(‘Is GrumpyCat grumpy?', default: true)
#=> Is GrumpyCat grumpy? yes
#=> true 
```

Enter fullscreen mode Exit fullscreen mode

[![grumpy cat](img/3bd8e7fd518227fee1e3e76358a31828.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--czkm_etk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.toiimg.com/thumb/imgsize-533580%2Cmsid-69374262%2Cwidth-400%2Cresizemode-4/69374262.jpg)

*   **和任何菜单方法**与`menu.default <default value(s)>`在菜单内迭代

```
prompt.select('Choose your fruit of choice') do |menu|
  menu.default 3

  menu.choice 'Papaya', 1
  menu.choice 'Mango', 2
  menu.choice 'Watermelon', 3
end 
```

Enter fullscreen mode Exit fullscreen mode

```
prompt.multi_select("Select tea?") do |menu|
  menu.default 2, 5

  menu.choice :green
  menu.choice :black
  menu.choice :white
  menu.choice :earl_grey
  menu.choice :sweet
end 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 要求

`:require`选项允许在用户继续下一步之前需要提示(你猜对了)。像`default:`一样，它可以被添加到任何提示方法中，这使得它们都是非常有用的特性。

```
prompt.mask('Enter a password:', required: true)
#Enter a password: 
#>> Value must be provided 
```

Enter fullscreen mode Exit fullscreen mode

```
prompt.ask('What is your name?', required: true)
#What is your name? 
#>> Value must be provided 
```

Enter fullscreen mode Exit fullscreen mode

不可否认，对于像 menu 方法这样的方法，无论是否需要，用户都必须在继续之前输入响应，因此将其固定在菜单提示的末尾是多余的。

* * *

#### 返回值

返回值默认为用户选择的任何选项的字符串，就像第一个 doge 例子一样。但是也可以通过在选择本身之后添加您想要的返回值来定制返回值，就像您在上面的水果示例中看到的:`menu.choice 'Papaya', 1`

* * *

## T2】

* * *

虽然我在这里声称这些方法是最有用的，但是 TTY::Prompt 还有更多可用的特性，我鼓励你充分探索他们的[文档页面](https://github.com/piotrmurach/tty-prompt#ttyprompt-)，给自己一个机会来使用它们。编码快乐！

* * *

*-编辑于 6 月 21 日，以表彰造物主。