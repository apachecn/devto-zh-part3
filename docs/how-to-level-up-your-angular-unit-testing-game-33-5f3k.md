# 如何升级你的角度单位测试游戏(3/3)

> 原文：<https://dev.to/alisaduncan/how-to-level-up-your-angular-unit-testing-game-33-5f3k>

这是角度单元测试系列的第三篇文章。如果您不熟悉使用 Angular CLI 或更改 Karma 配置，请阅读本系列的前两篇文章。

在上一篇文章中，我们学习了如何编辑 *karma.conf.js* 来添加记者和设置报道阈值。在这篇文章中，我们将创建共享的测试文件，并创建我们自己的参数来限制测试运行的文件。随着项目变得越来越大，运行所有的测试可能需要一段时间，所以这是一种简单的目标测试方法。是时候升级你的测试游戏，开始单元测试了！

[![](img/72b372ac6b44a8d2e17a5bf8b34ccd9d.png)](https://i.giphy.com/media/bdpIDWZFkI67S/giphy.gif)

> Angular CLI 没有公开 Karma 拥有的所有配置选项，所以如果目标测试不是你的事情，也许有其他方法可以定制 Angular CLI 测试。我展示这个例子是为了说明如何解决 Angular CLI 中的限制，而不是建议如何正确配置 Karma。我很乐意在评论中听到你定制 Angular CLI 单元测试的所有方式！

# 跟着一起走

我们使用 Angular CLI 测试应用程序，并使用我以前写的一篇文章中的代码。

[![alisaduncan](img/372f8b4f6ed7e4d403292148f8aace51.png)](/alisaduncan) [## 拦截 Http 请求——使用和测试 Angular 的 HttpClient

### 亚里沙 8 月 28 日 174 分钟阅读

#angular #testing #javascript #programming](/alisaduncan/intercepting-http-requests---using-and-testing-angulars-httpclient)

您也可以克隆[tutorial-angular-http client](https://github.com/alisaduncan/tutorial-angular-httpclient)并运行测试。帖子中讨论的所有关卡都可以在“测试-配置”分支中找到。

# 创建共享测试文件

随着你的应用程序的增长，你可能会发现你不止一次地创造了同样的假货。我们可以通过创建测试文件来使事情变得简单一些。

创建你的假的，并把它放在一个测试文件夹。为了方便起见，我将我的文件夹命名为“testing ”,并将其作为“src”目录的子目录。如果你在跟踪回购，我创建了一个*假用户服务。*

在 *$/src/tsconfig.app.json* 中，在“exclude”数组中添加“testing”文件夹。它需要匹配全球模式。所以在我的例子中，我有

```
"exclude":  [  "test.ts",  "**/testing/*",  "**/*.spec.ts"  ] 
```

Enter fullscreen mode Exit fullscreen mode

你的规格文件现在可以引用你的共享假货，而不是必须在文件中定义它！

我还喜欢在“testing”文件夹中添加一个快捷路径，这样我就不会有世界上最糟糕的导入语句了。在您的 *$/tsconfig.json* 中，在“libs”之后添加一个“paths”属性，以添加一个到您的测试文件夹的快捷方式，如下所示

```
"lib":  [  "es2018",  "dom"  ],  "paths":  {  "@testing/*":  ["src/testing/*"]  } 
```

Enter fullscreen mode Exit fullscreen mode

在规范文件中，我可以使用
导入我的假用户服务

```
import { FakeUserService } from '@testing/fake-user.service'; 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/3c44c5dfbfdf551c5a47fd2a5901044e.png)](https://i.giphy.com/media/24i3lhBG6zhVC/giphy.gif)

# 使用 Jasmine 结构进行目标测试

在深入定制代码进行测试之前，让我们从使用 Jasmine 的原生结构强制运行特定的测试开始。您可以通过在测试套件或测试前添加“f”来强制测试。

所以把`describe`改成`fdescribe`或者把`it`改成`fit`。那么只有带有' f '前缀的测试将会运行。您的测试输出仍然包括所有的测试(它跳过没有前缀的测试)。

你要记得去掉前缀“f”。幸运的是，您可以添加 lint 规则来记住删除强制测试。

在您的 *$/tslint.json* 中，添加一个名为“ban”的新规则。我们将像这样添加“适合”和“fdescribe:

```
"ban":  [  true,  ["fit"],  ["fdescribe"]  ] 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/3c87d7ec5c2fcb079c4eae39f1b76fa9.png)](https://i.giphy.com/media/nj0k2dDx4YAgg/giphy.gif)

但是，使用本机结构有其缺点。你必须编辑代码，并记住删除' f '前缀。此外，当您有很多测试时，仍然需要时间来完成测试运行，因为它仍然评估是否运行每个测试。那么您的输出充满了被跳过的测试用例。Boooo...幸运的是，我们可以解决这个问题。

## 通过限制文件进行目标测试

只运行您想要的文件的一种方法是编辑 regex Angular CLI 用来查找所有的 **。规格*文件。我们可以硬编码我们想要运行的测试文件。

在`$/src/test.ts`中，找到线

```
const context = require.context('./', true, /\.spec\.ts$/); 
```

Enter fullscreen mode Exit fullscreen mode

编辑正则表达式以定位您想要的测试文件，例如

```
const context = require.context('./', true, /\.service\.spec\.ts$/); 
```

Enter fullscreen mode Exit fullscreen mode

运行所有 *service.spec.ts* 文件。

[![](img/22218be4141fb5006b37f537895af149.png)](https://i.giphy.com/media/3o7527pa7qs9kCG78A/giphy.gif)

这种方法肯定能完成任务。它加速了测试运行，并且只在控制台输出中显示服务文件，但是现在我们没有 lint 安全来记住恢复我们所做的更改。还有另一个选项——创建一个自定义参数。

# 创建自定义参数

我想把试运行的文件交上去。我们可以添加自定义代码来实现这一点。有时候，揭开库的盖子，自己钻研代码是很有趣的。以更好地满足我们需求的方式把东西拆开再组装起来，有助于我们学习，让我们成为更好的工程师和工匠。

> 警告:Angular v7 有意限制传递给 Karma 的参数，因此此解决方案仅适用于 Angular v6。当你升级的时候，冒着它坏掉的风险按照说明去做，但是也许它会给你一些你可以尝试的事情的想法！

## 处理命令行参数

在 *karma.config.js* 中，我们需要解析命令行参数来获取测试文件。我准备把这个参数叫做`specs`。

在顶部 *karma.config.js* 定义一个变量来保存测试文件列表，并解析命令行参数来获取测试文件列表。我的代码看起来是这样的(是的，有点难看——当然可以清理):

```
 let specs = [];
if (process.argv.indexOf('specs') !== -1) {
  specs = process.argv.slice(process.argv.indexOf('specs') + 1);
  specs = specs.slice(0, specs.findIndex(el => el.includes('--')) === -1 ? specs.length : specs.findIndex(el => el.includes('--')));
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在`args`数组中添加`specs`:

```
client: {
      args: [specs],
      clearContext: false // leave Jasmine Spec Runner output visible in browser
}, 
```

Enter fullscreen mode Exit fullscreen mode

*karma.conf.js* 到此为止。

## 创建要测试的文件的正则表达式

所以现在我们可以传入一个文件列表来限制测试。我们在 *test.ts* 文件中这样做。

我们需要获得`karma`上下文，所以通过添加
来声明它

```
declare const __karma__: any; 
```

Enter fullscreen mode Exit fullscreen mode

作为导入语句后的第一行。

然后我们需要构建文件列表正则表达式。我从 karma 上下文中获取文件列表，并将文件添加到一个变量中。如果不限制文件，我们仍然希望运行所有测试。

```
const args = __karma__.config.args[0];
let fileRegex: RegExp = /.*/;
if ( args && args.length) {
  const files = args.join('|');
  fileRegex = new RegExp(`(${files})\.spec\.ts$`);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们希望使用`fileRegex`而不是定义的角度，所以我们之前硬编码服务测试的行变成了

```
const specFiles = context.keys().filter(path => fileRegex.test(path)); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们希望正确地加载模块，以考虑到`specFiles`

```
specFiles.map(context); 
```

Enter fullscreen mode Exit fullscreen mode

要查看上下文中的所有代码，这里是我们刚刚所做的要点。