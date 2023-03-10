# 创建 VS 代码扩展

> 原文：<https://dev.to/thorning_m/creating-a-vs-code-extension-4ppe>

在我工作的地方，我们已经变得非常特别，在导入部分和声明一些对象时，要让我们的代码垂直排列，这样看起来就很好，很整洁！我的同事评论说，如果有一个 VS 代码的扩展来为我们做比对就好了，所以我做了一个，我怀疑这一直是他的计划！。

完整的代码可以在我的 Github 上找到，我将在下面解释我创建它的步骤。

[![Gif of extension in action](img/e400fb13fb1c74dfa0880e09e5ef4a74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kNd56ib8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hellocode.dev/action-4ff01633b7d3b1fd843215287827b327.gif)

## 入门

第一步是用 NPM 下载 Yeoman 和 VS 代码扩展生成器:

```
npm install -g yo generator-code 
```

运行发电机支架一个准备开发的新项目。只需键入以下内容并回答问题:

```
$ yo code

   _-----_ ╭──────────────────────────╮
  | | │ Welcome to the Visual │
  |--(o)--| │ Studio Code Extension │
   `---------´ │ generator! │
  ( _´U`_ ) ╰──────────────────────────╯
  / ___A___ \ /
   | ~ |
   __'.___.'__
 ´ ` |° ´ Y `

? What type of extension do you want to create? New Extension (TypeScript)
? What's the name of your extension? my-new-extension
? What's the identifier of your extension? my-new-extension
? What's the description of your extension? It's an extension which is mine and new.
? Enable stricter TypeScript checking in 'tsconfig.json'? No
? Setup linting using 'tslint'? No
? Initialize a git repository? (Y/n) n 
```

一旦创建了项目并安装了依赖项，您就可以在 VS 代码中打开该文件夹来检查项目结构。按 F5 启动调试器，并在新的“扩展开发宿主窗口”中运行您的代码。VS Code 的团队在这里所做的很棒，因为你可以在主编辑器中放置断点，同时在 VS 代码的开发版本中尝试你正在编写的扩展。

这就是我要说的关于设置的所有内容，我还没有详细说明；如果你想要一篇关于入门的更深入的文章，那么我推荐你看看 VS Code 网站上的[这篇教程](https://code.visualstudio.com/api/get-started/your-first-extension)。

## 添加菜单项

我知道我想做的第一件事是向上下文菜单添加一个项目，以便用户可以右键单击一些突出显示的文本来运行扩展。这可以通过 *package.json* 来实现，只需在`contributes`部分添加以下内容即可。当你打开这个文件的时候，你也应该把所有对“helloWorld”的引用改成你的扩展名:

```
"contributes": {
  "commands": [
    {
      "command": "extension.alignVertically",
      "title": "Align Vertically"
    }
  ],
  "menus": {
    "editor/context": [
      {
        "command": "extension.alignVertically",
        "group": "YourGroup@1"
      }
    ]
  }
} 
```

## 编码分机

如果您选择用 JavaScript 编写您的扩展，那么您将有一个名为 *extension.js* 的文件，当您第一次打开它时，它看起来像这样:

[![Hello world sample](img/431d2f3e38902bd37894dbf9117bd880.png)](///static/f3be48c3a4e063344739747b86168528/66c5e/helloWorld.png)

如果你用 TypeScript 写你的文件，它看起来会有所不同，但是两者的逻辑是一样的。我们需要更改`activate`函数来注册我们的扩展，注意`"extension.alignVertically"`与*包中的条目相匹配。* 

```
function activate(context) {
  let disposable = vscode.commands.registerCommand(
    'extension.alignVertically',
    alignVertically
  )

  context.subscriptions.push(disposable)
} 
```

传递给`vscode.commands.registerCommand`的第二个参数是我们的函数，我们称之为`alignVertically`。这个函数将调用处理文本格式的函数，并在编辑器中替换它:

```
async function alignVertically() {
  const editor = vscode.window.activeTextEditor
  const text = editor.document.getText(editor.selection)
  if (text) {
    const keyword = await getKeywordFromUser() 
```

让我们仔细看看这个函数。在第 2 行的*上，我们从`vscode`对象中获取`activeTextEditor`(这是文件顶部所需要的)。然后，在*第 3 行*我们得到用户高亮显示的文本。第 4*行*上的`if`语句意味着只有当某些文本被选中时，程序的其余部分才会运行。*

接下来(*第 5 行*)我们提示用户输入将要用来分割文本的关键字。你可能在第 1 行的*上注意到这个函数是`async`，这意味着我们可以通过使用`await`命令让它等待一些数据。我们调用`getKeywordFromUser`，执行暂停，直到结果返回。下面是函数:* 

```
function getKeywordFromUser() {
  return vscode.window.showInputBox({
    placeHolder: "Align by which word?"
  });
} 
```

使用`vscode` API 的`window`对象上的一个方法，我们需要做的就是设置我们想要在输入框中显示的占位符文本。API 返回的结果是一个承诺，当用户点击回车键时，这个承诺被解析。

回到`alignVertically`功能:

```
if (text) {
  const keyword = await getKeywordFromUser()
  const lines = getLines(text, keyword)
  const mask = getMask(lines)
  const transformedText = transform(lines, mask, getSpaces)
  const result = joinWithKeyword(transformedText, keyword)
  editor.edit(builder => builder.replace(editor.selection, result))
} 
```

*第 6 - 9 行*调用我写的函数来转换高亮显示的文本。这些在另一个文件中，我将在下一节介绍它们。到了*第 10 行*，我们将转换后的文本保存在一个名为`result`的变量中，剩下要做的就是用这个`result`变量的值替换编辑器中高亮显示的文本。

第 2 行*中的`editor`变量有一个名为`edit`的方法，该方法调用一个带有方法`replace`的对象(此处命名为`builder`)的函数，正如您可能猜到的那样，它用`result`替换选定的文本(`editor.selection`——之前在第 3*行*中使用过)。唷，打出来的时候感觉很多，不过也没那么糟，希望读起来有意义！*

接下来的几节是关于格式化文本的函数。如果你只对如何创建一个扩展感兴趣，那么你应该跳过这些，跳到最后一部分，关于如何将你的扩展发布到市场。然而，如果你对我如何格式化文本本身感兴趣，那么请继续阅读！

## 测试驱动开发

*第 6 - 9 行*使用了从一个单独的文件导入的五个函数。这些函数通过以下方式处理文本:

1.  将文本分割成独立的块，这样我可以很容易地处理。
2.  计算每行中提供的关键字的当前位置。
3.  为每一行创建正确数量的空白空间的新块。
4.  向每一行添加新的空白空间块。
5.  将文本块重新连接在一起。

像这样分离逻辑的一个好处是，你可以编写简单的函数，每个函数只做一件事。这些是没有任何副作用的纯函数——根据你输入的内容，你确切地知道你将会得到什么。正因为如此，开发我们的函数的最好和最简单的方法是建立测试用例，在这些用例中，我们可以控制每个函数的输入，并测试输出是否符合预期。

VS 代码生成器创建已经安装了 Mocha 的项目，但是我更喜欢使用 [Jest](https://jestjs.io/) 。你可以和 NPM 一起安装 Jest:

```
npm install Jest -D 
```

这将把 Jest 添加到 *node_modules_ binaries (* )。bin_)文件夹中，那么运行 Jest 最简单的方法就是将这一行添加到您的*包. json* :
中的`scripts`部分

```
"scripts": {
  "test": "jest"
} 
```

并使用命令运行测试:

```
npm test 
```

Jest 将在您的代码库中搜索带有*的文件。名称中的 test* 并在这些文件中运行测试(我们的函数在一个文件中，我更确切地说是在一个名为 *functions.js* 的文件中，测试在一个同样名为 *functions.test.js* 的文件中)。您还可以在监视模式下启动测试，以便每次进行更改时测试都会重新运行；手表菜单包括一些值得探索的有用选项:

```
$ npm test -- --watch

No tests found related to files changed since last commit.
Press `a` to run all tests, or run Jest with `--watchAll`.

Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run. 
```

在文件的顶部我从 *functions.js* 中的函数`require`。我将这些函数放在一个单独的文件中的原因是为了在运行测试时不需要模仿任何东西；如果我把它们放在 *extension.js* 中，那么 Jest 会尝试导入`vscode`对象。

为了创建一个 Jest 测试套件，你需要使用一个`describe`块。第一个参数只是一个字符串，将在每个测试开始时使用，第二个参数是运行每个测试的函数。

```
const {
  getMask,
  getLines,
  transform,
  getSpaces,
  joinWithKeyword
} = require("../functions.js");

describe("The function ", () => {

  const keyword = "SPLIT";

  const text = [
    "This is some SPLIT text",
    "I want SPLIT to test",
    "Nothing to report here",
    "It SPLIT should be getting",
    "split where there SPLIT is"
  ].join("\n");

  const expectedLines = [
    ["This is some ", " text"],
    ["I want ", " to test"],
    ["Nothing to report here"],
    ["It ", " should be getting"],
    ["split where there ", " is"]
  ];

  const expectedMask = [13, 7, 0, 3, 18];

  const expectedTransformed = [
    ["This is some ", " text"],
    ["I want ", " to test"],
    ["Nothing to report here"],
    ["It ", " should be getting"],
    ["split where there ", " is"]
  ];

  const expectedResult = [
    "This is some SPLIT text",
    "I want SPLIT to test",
    "Nothing to report here",
    "It SPLIT should be getting",
    "split where there SPLIT is"
  ].join("\n"); 
```

首先，我设置了将在每个测试中使用的变量。在*的第 11 行*是我们的用户提供的虚构的关键字，接下来在*的第 13 行*是我们的用户高亮显示的文本块(声明为一个数组，用换行符`\n`连接)。然后，每个后续的`expected`输出既用于测试函数是否产生了正确的输出，也作为后续函数的输入。

```
test("getLines returns expected", () => {
    expect(getLines(text, keyword)).toEqual(expectedLines);
  });

  test("getMask returns expected", () => {
    expect(getMask(expectedLines)).toEqual(expectedMask);
  });

  test("getSpaces returns expected", () => {
    expectedMask.forEach(index => {
      const diff = 18 - index;
      expect(getSpaces(18, index).length).toBe(diff);
    });
  });

  test("transform returns expected", () => {
    expect(transform(expectedLines, expectedMask, getSpaces)).toEqual(
      expectedTransformed
    );
  });

  test("joinWithKeyword returns expected", () => {
    expect(joinWithKeyword(expectedTransformed, keyword)).toEqual(
      expectedResult
    );
  }); 
```

如果一个测试失败了，Jest 会给你一个方便的比较来告诉你为什么失败了:

[![Failed tests output](img/1f023f97fe8fd95e952030edb69aa7f7.png)](///static/389ebce48463e4436e305c1adb543253/ecd27/failedTest.png)

可以设置 VS 代码调试器，这样您就可以在测试运行时对代码进行断点设置，以帮助调试。我不会在这里详细说明如何做到这一点，也许在另一篇文章中！

这是我们的目标，所有测试都通过了:

[![Passed tests output](img/58c4a75bfc2a1c267fd86e7b045a2481.png)](///static/800590fe4ba19be3fd0ab1348ac5e79a/f3361/passedTest.png)

## 格式化文本

就是(终于！)是时候看看函数本身了。这里提醒一下来自 *extensions.js* :
的*行 6 - 9*

```
const lines = getLines(text, keyword)
const mask = getMask(lines)
const transformedText = transform(lines, mask, getSpaces)
const result = joinWithKeyword(transformedText, keyword) 
```

#### **【getLines(文本，关键字)**

```
getLines(text, keyword) {
  return text.split("\n").map(line => line.split(keyword));
} 
```

`getLines`函数将高亮显示的文本和关键字作为参数，并使用 Array 的 split 方法来拆分换行符上的文本(还记得上面我们是如何测试的吗？)，然后我们在关键字处拆分每一行，得到一个数组，其中每个元素都是一行文本，它本身是一个数组，每个元素都包含关键字之前或之后的文本。例如，如果提供的关键字是“SPLIT ”,则显示以下文本:

这是一些拆分文本

它会通过换行符
和关键词
来拆分单词

将输出为:

```
[['this is some ', 'text'], ['It will ', 'words by newline'], ['Also by keyword'] ] 
```

#### **【get mask(线条)**

```
getMask(lines) {
  return lines.map(line => {
    if (line.length > 1) {
      return line[0].length;
    }
    return 0;
  });
} 
```

这个函数创建一个每行一个元素的数组。每个元素都包含一个数字，表示关键字前面的文本块的长度。如果关键字不在一行上，它会在它的位置上放一个零。上例中的掩码是`[13, 8, 0]`。

#### **【get space(max，mask[i])**

```
getSpaces(max, index) {
  const diff = max - index;
  return new Array(diff).fill("  ");
} 
```

该功能由`transform`功能使用(如下)。它使用`max`变量(接下来解释)并使用它来计算文本块需要增加多少空间。它返回所需长度的数组并用空格填充。

#### **【变换(线条、遮罩、获取空间)**

```
transform(lines, mask, getSpaces) {
  const max = Math.max(...mask);
  return lines.map((line, i) => {
    if (mask[i]) {
      const extended = [line[0], ...getSpaces(max, mask[i])].join("");
      line.splice(0, 1, extended);
    }
    return line;
  });
} 
```

这个函数做的第一件事是从`mask`中获取`max`号。这是文本行中最右边的关键字的位置，也是我们要将其他行移到的位置。接下来，它映射通过`lines`数组，如果该行在`mask`中有相应的数字(即。大于零)，则它创建文本块的扩展版本，其中添加了额外的空格。

这发生在*5 号线*。您可以看到，我们从第一个元素(`line[0]`)创建了一个新数组，并将上面的`getSpaces`函数的输出传播到其中。这个数组看起来像:

```
['this is some ', '  ', '  ', '  ', '  ', '  ', '  ', '  ', '  ', '  ', '  '] 
```

然后我们加入数组，形成一个以空格结尾的字符串。在*第 6 行*上，我们使用数组的拼接方法用我们新的扩展版本替换该行的第一个元素。

#### **【joinvithkeyword(transformed text，keyword)】**

```
joinWithKeyword(transformed, keyword) {
  return transformed.map(l => l.join(keyword)).join("\n");
} 
```

最后，我们只需要把我们的文本块放回一起。我们通过`transform`函数映射数组输出，并用`keyword`连接每一行。最后，我们再次使用换行符(`\n`)将这些行连接起来。

这就是我们创建扩展所需要做的一切。剩下要做的就是让人们可以使用它。

## 发布扩展

我不打算详细讨论这个问题，一部分是因为 VS Code 网站上的文档告诉了你需要知道的一切，另一部分是因为我觉得我已经唠叨了太久了！因此，如需详细指南，请查看[这里的](https://code.visualstudio.com/api/working-with-extensions/publishing-extension)。简而言之就是你需要跑:

```
npm install -g vsce 
```

然后你需要从 Azure DevOps 站点获取一个个人访问令牌。您使用这个令牌通过运行:
将自己设置为发布者

```
vsce create-publisher < publisher name > 
```

一旦`vsce`(代表 Visual Studio 代码扩展)安装完毕，您就可以运行下面的代码将您的扩展发布到市场:

```
vsce publish 
```

如果您不想将您的扩展发布到市场供公众使用，那么您可以运行:

```
vcse package 
```

这将创建一个*。vsix* 文件，可以与你选择的任何人共享，并且很容易安装:

```
code --install-extension < path/to/file.vsix > 
```

* * *

当我弄清楚如何进行这个基本的扩展时，我花了一点时间来寻找我需要的信息。我希望如果你偶然看到这篇文章，它能帮助你填补一两个空白。如果您有任何意见或问题，或者发现了一些不正确的或可以用更好的方式来做的事情，请在 Twitter 上告诉我。干杯😄