# 铸造🍃:创建包

> 原文：<https://dev.to/gdotdesign/mint-creating-a-packages-5e9b>

*这是展示 Mint 特性的系列文章中的第六篇，你可以在这里找到之前的文章:*

*   *[薄荷🍃:入门](https://dev.to/gdotdesign/getting-started-with-mint-3k2o)T3】*
*   *[薄荷🍃:组件](https://dev.to/gdotdesign/components-in-mint-4a4n)T3】*
*   *[薄荷🍃:组件的事件和状态](https://dev.to/gdotdesign/mint-events-and-state-of-components-3j3a)*
*   *[薄荷🍃:处理 HTTP 请求](https://dev.to/gdotdesign/mint-handling-http-requests-2ep3)T3】*
*   *[薄荷🍃:造型元素](https://dev.to/gdotdesign/mint-styling-elements-295o)*

在这篇文章中，我将向你展示如何创建一个包来与他人分享。

* * *

Mint 内置了对包的支持——在任何公共 Git 存储库上共享的代码——这允许你与他人共享组件、模块甚至存储。

## 创建一个包

在这篇文章中，我们将为一个有内置计数器的`textarea`创建一个包。

首先我们需要创建一个新的 Mint 应用程序(它本身是一个包):

```
➜  Projects git:(master) ✗ mint init mint-textarea-counter    
Mint - Initializing a new project
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚙ Creating directory structure...
⚙ Writing initial files...

There are no dependencies!

There is nothing to do!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 
```

Enter fullscreen mode Exit fullscreen mode

## 添加一个组件

我们将为名为`Textarea.Counter` ( `source/Texarea.Counter.mint` ):
的`textarea`创建一个组件，而不是`Main`

```
component Textarea.Counter {
  property onChange : Function(String, a) = (value : String) : Void { void }
  property value : String = ""

  style base {
    border: 1px solid #DDD;

    flex-direction: column;
    display: inline-flex;
  }

  style textarea {
    font-family: sans-serif;
    font-size: 16px;

    padding: 10px;
    margin: 0;
    border: 0;
  }

  style counter {
    font-family: sans-serif;
    background: #EEE;
    font-size: 14px;
    padding: 10px;
  }

  fun handleChange (event : Html.Event) : a {
    event.target
    |> Dom.getValue()
    |> onChange()
  }

  fun render : Html {
    <div::base>
      <div::counter>
        "Character Count: "

        <{
          value
          |> String.size()
          |> Number.toString()
        }>
      </div>

      <textarea::textarea
        onChange={handleChange}
        value={value}/>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*在开发期间，我建议创建一个`Main`组件用于测试，它没有被添加到 Git 存储库中。*

## 创建 Git 存储库

为了与他人共享这个组件，我们需要创建一个 Git 资源库，在这种情况下，我们将在 Github 上创建一个，我们需要将我们的代码推送到这个资源库:

```
➜  mint-textarea-counter ✗ git init
Initialized empty Git repository in /home/.../mint-textarea-counter/.git/

➜  mint-textarea-counter git:(master) ✗ git remote add origin .../mint-textarea-counter.git

➜  mint-textarea-counter git:(master) ✗ git add .

➜  mint-textarea-counter git:(master) ✗ git commit -m "Initial commit."
[master (root-commit) 5250277] Initial commit.
 3 files changed, 60 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 mint.json
 create mode 100644 source/Textarea.Counter.mint

➜  mint-textarea-counter git:(master) git push origin HEAD 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经建立了存储库，接下来的事情是为第一个版本创建一个标签:

```
➜  mint-textarea-counter git:(master) git tag 1.0.0
➜  mint-textarea-counter git:(master) git push origin HEAD --tags           
Total 0 (delta 0), reused 0 (delta 0)
To github.com:mint-lang/mint-textarea-counter.git
 * [new tag]         1.0.0 -> 1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

现在这个包已经可以使用了。

## 使用包

在另一个 Mint 应用程序中，我们可以通过将它定义为`mint.json` :
中`dependencies`字段的依赖项来使用这个包

```
{
  ...

  "dependencies": {
    "mint-textarea-counter": {
      "repository": "https://github.com/mint-lang/mint-textarea-counter",
      "constraint": "1.0.0 <= v < 2.0.0"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将解释上述代码的含义:

*   我们已经定义了依赖关系:`mint-textarea-counter`(这必须匹配包`mint.json`中的`name`字段)
*   我们使用`repository`字段指定了它所在的 Git 存储库
*   我们在`constraint`字段中指定了版本约束

之后，我们只需要用`mint install`命令安装依赖项:

```
➜  test git:(master) ✗ mint install
Mint - Installing dependencies
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚙ Constructing dependency tree...
  ✔ Cloned mint-textarea-counter(https://github.com/mint-lang/mint-textarea-counter.git)

⚙ Resolving dependency tree...
  ◈ mint-textarea-counter ➔ 1.0.0

⚙ Copying packages...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
All done in 1.231s! 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以以同样的方式使用组件，如果它是在项目中定义的:

```
component Main {
  state value : String = "Initial value..."

  fun handleChange (value : String) : Promise(Never, Void) {
    next { value = value }
  }

  fun render : Html {
    <Textarea.Counter
      onChange={handleChange}
      value={value}/>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以在这里找到存储库:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [薄荷郎](https://github.com/mint-lang) / [薄荷区专柜](https://github.com/mint-lang/mint-textarea-counter)

### 共享组件的示例包。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 造币厂-文本区-柜台

这个存储库是一个全新包的例子。

</article>

[View on GitHub](https://github.com/mint-lang/mint-textarea-counter)

今天就到这里，谢谢你的阅读🙏

* * *

如果你想了解更多关于 Mint 的知识，请查看[指南](https://guide.mint-lang.com)📖

在下一部分，我将向您展示如何[使用内置路由系统](https://dev.to/gdotdesign/mint-routing-2h69)😉在那里见👋