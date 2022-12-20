# 测试驱动的发现:通过编写测试来发现代码做了什么

> 原文：<https://dev.to/bengreenberg/test-driven-discovery-finding-out-what-code-does-by-writing-tests-3imk>

你被分配到一个新的代码库，你试图弄清楚每个方法是做什么的。其中一些比其他的更简单，通过快速阅读实际代码就可以知道它们的用途。与此同时，其他方法要复杂得多，并且在应用程序中有更多相互关联的依赖关系，这使得理解它们的目的更具挑战性。

当遇到对您来说是新的代码时，理解它的最好方法之一是为它编写测试。测试驱动开发(TDD)假设在编写代码之前编写测试，让开发人员在编程之前先确定行为。在我们正在讨论的场景中，我们关心的是我们可以称之为**测试驱动发现(TDDo)** 的东西，在这里我们从事一些侦查工作，并且我们使用测试来做我们的调查。

[![](img/97c6c12b2f4679cb78ed4ad4b57b274e.png)](https://i.giphy.com/media/3orieUe6ejxSFxYCXe/giphy.gif)

让我们看一个例子。以下是[降价过滤器](https://github.com/Nexmo/nexmo-developer/blob/master/app/filters/label_filter.rb)的代码。

您可以在 Nexmo Developer 上找到所有代码:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[NEX mo](https://github.com/Nexmo)/[NEX mo-开发者](https://github.com/Nexmo/nexmo-developer)

### 为使用 Nexmo API 平台的开发人员提供资源

<article class="markdown-body entry-content container-lg" itemprop="text">

# Vonage API 开发人员门户

[![Build Status](img/1657d7a568d57f12bfacba18653156bb.png) ](https://travis-ci.org/Nexmo/nexmo-developer/) [ ![MIT licensed](img/486bafc059df9137c69818cd66696105.png)](https://github.com/Nexmo/nexmo-developer./LICENSE.txt)

这个存储库是[https://developer.nexmo.com](https://developer.nexmo.com)的内容，包括 Vonage 文档、API 参考、SDK、工具&社区内容。要获得 Vonage 账户，请在 nexmo.com 免费注册[。](https://dashboard.nexmo.com/sign-up?utm_source=DEV_REL&utm_medium=github&utm_campaign=nexmo-developer)

### [测试](https://github.com/Nexmo/nexmo-developer#testing) [本地运行](https://github.com/Nexmo/nexmo-developer#running-locally) [管理仪表板](https://github.com/Nexmo/nexmo-developer#admin-dashboard) [故障排除](https://github.com/Nexmo/nexmo-developer#troubleshooting) [投稿](https://github.com/Nexmo/nexmo-developer#contributing) [许可](https://github.com/Nexmo/nexmo-developer#license)

## 测试

### 拼写检查

我们用美国英语编写文档，并在构建时通过 CI 检查强制执行。您可以使用以下命令在本地运行检查:

```
yarn spellcheck 
```

或者，如果您使用 Docker:

```
docker-compose exec web yarn spellcheck db:migrate 
```

如果有一个单词不在字典中，但使用正确，就把它添加到`.spelling`文件中(这里有很多例外，包括`Vonage`！)

### 散文风格检查

我们检查我们的内容是否有任何攻击性的、有能力的或性别化的语言，并在构建时通过 CI 检查强制执行。你可以跑…

</article>

[View on GitHub](https://github.com/Nexmo/nexmo-developer)

```
class LabelFilter < Banzai::Filter
  def call(input)
    input.gsub(/\[([a-zA-Z0-9\s:\-\.]+)\]/) do |_s|
      "<span class='Vlt-badge #{class_name($1)}'>#{$1}</span> "
    end
  end

  private

  def class_name(text)
    case text
    when 'POST'
      'Vlt-badge--green'
    when 'GET'
      'Vlt-badge--blue'
    when 'DELETE'
      'Vlt-badge--red'
    when 'PUT'
      'Vlt-badge--yellow'
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们从一开始就知道一些关于这段代码的事情:

*   它定义了一个名为`LabelFilter`的类
*   该类有一个名为`call`的方法，它接受一个参数
*   `call`方法使用 regex 查找并替换一个带有 HTML `span`标签的字符串，该标签根据提供给该方法的输入具有可变的类名
*   有一个名为`class_name`的私有方法，它接受一个参数，由一个 case 语句组成，该语句根据输入文本输出文本。

然而，尽管知道所有这些，我们对这些代码的实际行为却知之甚少。它实际上是做什么的？

我们怎么知道呢？让我们构建一些测试！

我们将使用我们的测试来查看我们提供的文本到底发生了什么。首先，`call`方法对它提供的随机文本字符串做了什么？

```
require 'rails_helper'

RSpec.describe LabelFilter do 
  it 'does something with some text' do
    input = "some text"

    puts described_class.call(input) 
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行 RSpec 时会得到什么结果？

```
some text 
```

Enter fullscreen mode Exit fullscreen mode

我们看到，如果输入是任何随机文本，那么结果输出将是完全相同的文本，没有以任何方式被触摸或转换。让我们继续并相应地改变我们第一个测试的写法:

```
it 'does not transform a random string' do
  input = "some text"

  expected_output = "some text"

  expect(described_class.call(input)).to eq(expected_output)
end 
```

Enter fullscreen mode Exit fullscreen mode

当我们此时运行 RSpec 时，我们的测试将会通过。重要的是要记住，我们编写测试是为了发现现有代码的行为，而不是描述未来代码的行为。这些测试是我们的夏洛克。我们现在发现随机的文本字符串不会被`LabelFilter`修改。

[![](img/ceed1fd23554784d58da5456f2447adb.png)](https://i.giphy.com/media/RaLIOPl8MLyWA/giphy.gif)

匹配正则表达式的文本呢？分解 Ruby regex 的一个很棒的工具是 [Rubular](http://rubular.com/) 。如果我们将代码中的表达式复制并粘贴到 Rubular 中，我们会看到括号中的字母数字字符串将会匹配。

如果我们改变输入来匹配正则表达式会发生什么？

让我们添加另一个测试:

```
it 'does something with a string inside brackets' do
  input = "[some text]"

  puts described_class.call(input)
end 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行 RSpec 时，我们得到如下结果:

```
<span class='Vlt-badge '>some text</span> 
```

Enter fullscreen mode Exit fullscreen mode

这是为什么呢？如果我们查看私有方法`class_name`,我们会看到用户输入被传递给这个方法，并在 case 语句中进行评估。有四种可能性，但我们提供的不是其中之一。因此，返回的是`span`的第一部分，因为它被明确地拼写为:`<span class='Vlt-badge ...`，但是后半部分依赖于 case 语句，因为我们的文本在那里没有匹配，所以我们返回空白:`<span class='Vlt-badge '>`。

我们现在可以修改我们的测试，以预期当前编写的代码的行为:

```
it 'returns an HTML span tag when provided with random text inside brackets' do
  input = "[some text]"

  expected_output = "<span class='Vlt-badge '>some text</span> "

  expect(described_class.call(input)).to eq(expected_output)
end 
```

Enter fullscreen mode Exit fullscreen mode

当我们提供匹配正则表达式的文本时会怎样？

```
it 'does something with "[POST]" input' do
  input = "[POST]"

  puts described_class.call(input)
end 
```

Enter fullscreen mode Exit fullscreen mode

这返回:

```
<span class='Vlt-badge Vlt-badge--green'>POST</span> 
```

Enter fullscreen mode Exit fullscreen mode

我们看到它在一个被赋予类名“Vlt-badge Vlt-badge - green”的`<span>`标签中返回单词“POST”。这符合我们在读取`class_name`私有方法时的预期。

如果我们不提供括号，但提供了匹配的单词，那该怎么办？这给我们返回的只是这个方法没有改变的单词。

现在我们知道了这两件事，我们还有两个测试:

```
it 'does not transform a matching string if it is not inside brackets' do
  input = "POST"

  expected_output = "POST"

  expect(described_class.call(input)).to eq(expected_output)
end

it 'returns a green HTML class when string "[POST]" is provided and puts "POST" in between <span> tags stripped of brackets' do
  input = "[POST]"

  expected_output = "<span class='Vlt-badge Vlt-badge--green'>POST</span> "

  expect(described_class.call(input)).to eq(expected_output)
end 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们对**这段代码实际上做了什么有了一个很好的想法。现在我们要确保我们用测试来覆盖其余的情况，以确认我们的假设:** 

```
it 'converts [POST] to a green label' do
  input = "[POST]"

  expected_output = "<span class='Vlt-badge Vlt-badge--green'>POST</span>"

  expect(described_class.call(input)).to eq(expected_output)
end

it 'converts [GET] to a blue label' do
  input = "[GET]"

  expected_output = "<span class='Vlt-badge Vlt-badge--blue'>GET</span>"

  expect(described_class.call(input)).to eq(expected_output)
end

it 'converts [DELETE] to a red label' do
  input = "[DELETE]"

  expected_output = "<span class='Vlt-badge Vlt-badge--red'>DELETE</span>"

  expect(described_class.call(input)).to eq(expected_output)
end

it 'converts [PUT] to a yellow label' do
  input = "[PUT]"

  expected_output = "<span class='Vlt-badge Vlt-badge--yellow'>PUT</span>"

  expect(described_class.call(input)).to eq(expected_output)
end 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行这些测试时，我们看到它们都通过了。我们的测试驱动发现方法已经为我们提供了许多关于这段代码的信息。我们从对代码结构有了一个基本的轮廓，到对代码本身的行为有了一个更清晰的了解。我们还可以建立更多的测试来探索行为预期的边缘。例如，如果用户在括号内输入正确的字符串，但它是小写的还是大写的，会发生什么？

```
it 'does something with "[post]"' do
  input = '[post]'

  puts described_class.call(input)
end 
```

Enter fullscreen mode Exit fullscreen mode

结果呢？我们得到:`<span class='Vlt-badge '>post</span>`。这似乎不是期望的结果。当我们构建测试来发现代码的行为时，当我们遇到不想要的行为时，我们该怎么办？作为我们调查的结果，我们是否修改行为以符合我们认为代码应该做的事情？

我的建议是记录这种行为，并将其作为一个问题进行审查。也许这种行为是有原因的，而你却没有考虑到？如果没有，那么可以处理边缘情况，但是此时，我们的目标是发现现有的行为，而不是在我们描述它的同时修改它。因此，我们可以这样写这个测试:

```
# possible undesired behavior, to be discussed
it 'returns a non-color HTML span tag with  "post" in between the tags when "[post]" is provided' do 
  input = "[post]"

  expected_output = "<span class='Vlt-badge '>post</span> "

  expect(described_class.call(input)).to eq(expected_output)
end 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们完成了这个过程，我们就可以摘下侦探的帽子，看看我们完成了什么。我们从对现有代码一无所知发展到现在了解它能做什么，不能做什么，也许还能找到以后要解决的领域。如果这还不够，我们不仅扩展了对这段代码的理解，还扩展了我们的测试覆盖范围！

测试驱动的发现让我们走过我们不熟悉的代码，并获得更深的理解。下一次当你面对现有的代码时，为什么不试一试呢？

如果您感兴趣，请查看这些测试的[拉请求](https://github.com/Nexmo/nexmo-developer/pull/1329)。