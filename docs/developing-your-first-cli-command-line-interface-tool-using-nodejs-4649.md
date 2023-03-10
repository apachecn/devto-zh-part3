# 使用 NodeJS 开发您的第一个 CLI(命令行界面)工具

> 原文：<https://dev.to/lucifer1004/developing-your-first-cli-command-line-interface-tool-using-nodejs-4649>

大多数程序员更喜欢 CLI 而不是 GUI，为什么？

*   它们的尺寸较小。
*   批量处理很容易。
*   设计良好的自动补全可以防止不正确的操作。
*   使用 GUI 并不酷。(对于大多数极客来说都是如此。)
*   ...还有很多其他我还没想到的原因。

但是没有多少人真正开发了 CLI。幸运的是，在几个软件包的帮助下，用 NodeJS 构建 CLI 变得毫不费力。

以下是这篇文章的配套报道:

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[pkuosa-Gabriel](https://github.com/pkuosa-gabriel)/[node-CLI-starter](https://github.com/pkuosa-gabriel/node-cli-starter)

### 这是构建基于 node.js 的 CLI 工具的入门。

<article class="markdown-body entry-content" itemprop="text">

# 使用 NodeJS 开发您的第一个 CLI(命令行界面)工具

大多数程序员更喜欢 CLI 而不是 GUI，为什么？

*   它们的尺寸较小。
*   批量处理很容易。
*   设计良好的自动补全可以防止不正确的操作。
*   使用 GUI 并不酷。(对于大多数极客来说都是如此。)
*   ...还有很多其他我还没想到的原因。

但是没有多少人真正开发了 CLI。幸运的是，在几个软件包的帮助下，用 NodeJS 构建 CLI 变得毫不费力。

我们将使用的主要软件包:

*   [指挥官](https://github.com/tj/commander.js)
*   [shelljs](https://github.com/shelljs/shelljs)
*   [pkg](https://github.com/zeit/pkg)

## 开始之前

如果你不熟悉 NodeJS，或者 JavaScript，那也没关系，因为只要你有一些基本的编程经验，这个指令并不难。

但是，您需要安装一些必要的工具。如果您使用的是 MacOS，并且已经安装了 [homebrew](https://brew.sh/) ，那么这将非常简单:

```
brew
```

…</article>

[View on GitHub](https://github.com/pkuosa-gabriel/node-cli-starter)

我们将使用的主要软件包:

*   [指挥官](https://github.com/tj/commander.js)
*   [shelljs](https://github.com/shelljs/shelljs)
*   [pkg](https://github.com/zeit/pkg)

## 出发前

如果你不熟悉 NodeJS，或者 JavaScript，那也没关系，因为只要你有一些基本的编程经验，这个指令并不难。

但是，您需要安装一些必要的工具。如果您使用的是 MacOS，并且已经安装了 [homebrew](https://brew.sh/) ，那么这将非常简单:

```
brew install node yarn # Install node (the NodeJS engine) and yarn (a package manager for NodeJS) 
```

也可以使用`npm`，它是 NodeJS 的官方包管理器。我用`yarn`是出于个人喜好。它们的用法有些不同，但是通过帮助不难发现。

如果你使用的是 Linux 或 Windows，互联网上有大量的博客和文章，所以你可以去搜索如何在你的系统上安装 node 和 yarn。

安装完成后，我们可以进入我们的主要阶段。

## 步骤 00:设置存储库

最快的方法是克隆我的回购协议:

```
git clone https://github.com/pkuosa-gabriel/node-cli-starter my-cli
cd my-cli
git checkout step-00-repo-init
yarn install 
```

除了上面提到的包，为了方便你，我还配置了[更漂亮的](https://github.com/prettier/prettier)、[皮棉阶段的](https://github.com/okonet/lint-staged)和[哈士奇](https://github.com/typicode/husky)。如果你不想要或不喜欢它们，只需运行`yarn remove <package-name>`并删除相关代码，即`.prettierrc`、`.lintstagedrc`和`package.json`中的`'husky'`对象。

或者如果你想从头开始:

```
mkdir my-cli
cd my-cli
yarn init # You will need to answer several questions interactively
yarn add commander shelljs
yarn add -D pkg 
```

## 步骤 01:[指挥官](https://github.com/tj/commander.js)您好

每次学习新的东西，都会有一些“Hello world”的东西。这次也不例外。我们的第一个目标是构建一个输出“Hello world”的命令。

如果你是按照我的回购，你现在应该检查到下一个分支机构。

```
git checkout step-01-hello-world 
```

或者你可以用你最喜欢的 IDE 编辑`index.js`:

```
// index.js

/** 
 * This is the common way to import a package in NodeJS.
 * The CommonJS module system is used.
 */ 
const mycli = require('commander')

/**
 * () => {} is an arrow function, which belongs to the ES6 (ESMAScript 6) standard.
 */
mycli.action(() => {
  console.log('Hello world') // Print 'Hello world' to the command line.
})

/**
 * This line is necessary for the command to take effect.
 */
mycli.parse(process.argv) 
```

然后我们可以通过运行以下命令来验证它:

```
node index.js
#=> Hello world

node index.js hello
#=> Hello world 
```

请注意，额外的参数在这里没有什么不同，因为我们还没有使用它们。

在这个代码片段中，`action`决定了命令被触发后将执行什么。然而，在调用`parse`之前它不会被执行，T1 解析来自`process.argv`的输入参数。

例如，`node index.js`将被解析为:

```
Command  {  commands:  [],  options:  [],  _execs:  {},  _allowUnknownOption:  false,  _args:  [],  _name:  'index',  Command:  [Function:  Command],  Option:  [Function:  Option],  _events:  [Object:  null  prototype]  {  'command:*':  [Function:  listener]  },  _eventsCount:  1,  rawArgs:  [  '/usr/local/Cellar/node/11.6.0/bin/node',  '/path/to/my-cli/index.js'  ],  args:  []  } 
```

## 步骤 02:添加一些选项

hello-world 版本的 CLI 是无用的，因为它忽略我们输入的任何内容，只输出“Hello world”。为了让它更有用，我们将添加一些选项。

```
git checkout step-02-add-options 
```

或者您可以手动完成:

```
// index.js

/**
 * This is the common way to import a package in NodeJS.
 * The CommonJS module system is used.
 */

const mycli = require('commander')

/**
 * This arrow function is used for generating our bot's replies.
 * @param {string} word The intended output
 */
const bot = word => {
  console.log('The bot says:', word)
}

/**
 * This function is used for collecting values into the array.
 * @param {string} val The new value to be pushed into the array
 * @param {array} arr The original array
 * @return {array} The new array
 */
const collect = (val, arr) => {
  arr.push(val)
  return arr
}

mycli
  .option('-u, --username <name>', `specify the user's name`)
  .option('-a, --age [age]', `specify the user's age`)
  .option(
    '-g, --gender [gender]',
    `specify the user's gender`,
    /^(male|female)$/i,
    'private',
  )
  .option('-i, --additional-info [info]', 'additional information', collect, [])
  .option('-s, --silent', 'disable output')
  .option('--no-gender-output', 'disable gender output')
  .action(() => {
    if (!mycli.silent) {
      /**
       * `...` is called a template string (aka template literal). Expressions can be evaluated in a
       * template string, by using ${}, which is very similar to what we do in the command line with shell
       * scripts.
       * Here we use JS's internal function typeof to get the variable's type.
       * We also use ternary operator instead of if ... else ... for simplicity.
       */
      const nameLine = `Hello ${
        typeof mycli.username === 'string' ? mycli.username : 'world'
      }`
      bot(nameLine)

      const ageLine =
        typeof mycli.age === 'string'
          ? `I know you are ${mycli.age}`
          : 'I do not know your age'
      bot(ageLine)

      /**
       * Here we combine use of arrow function and IIFE (Immediately Invoked Function Expression).
       */
      if (mycli.genderOutput) {
        const genderLine = (() => {
          switch (mycli.gender) {
            case 'male':
              return 'You are a man'
            case 'female':
              return 'You are a woman'
            default:
              return 'Well, gender is your privacy'
          }
        })()
        bot(genderLine)
      }

      /**
       * Array.forEach is an easy way to perform iterative execution to all elements in an array.
       */
      mycli.additionalInfo.forEach(info => {
        const infoLine = `I also know ${info}`
        bot(infoLine)
      })
    }
  })

/**
 * This line is necessary for the command to take effect.
 */
mycli.parse(process.argv) 
```

相当多的变化！别怕，我会一一给你解释。

总共增加了 6 个不同的选项来帮助你全面了解如何使用 [commander](https://github.com/tj/commander.js) 。

在看我的解释之前，你可以先试一试。只需在命令行中键入`node index.js -h`或`node index.js --help`，就会看到自动生成的帮助消息。你不需要在你的代码中做任何事情，因为[指挥官](https://github.com/tj/commander.js)会为你处理它。您还可以自定义您的帮助消息。详细内容可以参考[指挥官](https://github.com/tj/commander.js)的公文中的[部分](https://github.com/tj/commander.js#custom-help)。

```
Usage: index [options]

Options:
  -u, --username <name>         specify the user's name
  -a, --age [age]               specify the user's age
  -g, --gender [gender]         specify the user's gender (default: "private")
  -i, --additional-info [info]  additional information (default: [])
  -s, --silent                  disable output
  --no-gender-output            disable gender output
  -h, --help                    output usage information 
```

示例输入:

```
node index.js -u Tom -a 18 -g male -i "Michael Jordan is the God of basketball." 
```

示例输出:

```
The bot says: Hello Tom // (name)
The bot says: I know you are 18 // (age)
The bot says: You are a man // (gender)
The bot says: I also know Michael Jordan is the God of basketball. // (additionalInfo) 
```

*   如果给定一个名字，机器人将以“Hello”开始，否则它将简单地说“Hello world”。
*   如果给出了年龄，机器人会说“我知道你是”，否则它会说“我不知道你的年龄”
*   如果给出了性别(男性/女性)，机器人会通过说“你是男性/女性”来复述，否则它会说“嗯，性别是你的隐私”
*   如果给出了附加信息，机器人将简单地回答“我也知道”。

如果你对 NodeJS 或者 JavaScript 不是那么熟悉，评论里有一些简单的介绍。进一步的细节，你可以转到 [NodeJS Doc](https://nodejs.org/en/docs/) ，或者其他网站如 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript) 和 [w3schools](https://www.w3schools.com/js/) 。

现在让我们看看代码。我们使用`.option()`为我们的 CLI 命令添加选项。作为一个函数，它接收几个参数:

1.  `flags`:类似'-u，- username '的字符串，指定该选项如何触发，以及是否有参数。这是**要求的**。
2.  `description`:描述该选项的字符串，将被收集到自动帮助消息中。这是*可选的*。
3.  `fn`:函数或正则表达式，将应用于该选项的输入参数。这是*可选的*。
4.  `defaultValue`:该选项参数的默认值。这是*可选的*。

[commander](https://github.com/tj/commander.js) 会将每个选项转换成 commander 对象的一个键(在我们的例子中是`mycli`)。它遵循以下原则:

1.  如果`flags`设置为`-c`，并且没有`--`标志，则转换为`mycli.C`。
2.  如果设置了`--`标志，例如`--username`，在这种情况下，无论是否设置了`-`标志，该选项都将转换为`mycli.username`。
3.  如果使用多个单词，例如`--additional-info`，它将被转换为骆驼形式`mycli.additionalInfo`。
4.  如果一个选项没有被使用，也没有给出默认值，那么它的值将是`undefined`。如果使用它，但没有给定参数，它的值将是`true`。
5.  特别是，如果设置了`--no`标志，例如`--no-gender-output`，它将被转换为`mycli.genderOutput`，而它具有不同的行为。使用该选项时，其值为`false`，不使用时为`true`。
6.  如果通过`[]`或`<>`给定了参数定义，并且在使用选项时给定了参数，那么该值将是参数(或来自`fn`的返回值，它将该参数作为其输入)，而不是布尔值。

> **【提示】**
> 
> *   避免使用`--name`，因为`mycli.name`已经存在。
> *   避免同时使用`-c`和`-C`而不为它们设置`--`标志，因为它们都将被转换为`mycli.C`。也要避免对不同的选项使用相同的`--`标志，或者像`--happy`和`--no-happy`这样的东西。记住 option= > key transform 的机制，那么你就再也不会犯这样的错误了。
> *   除了上面的第五点，用`--no`标志定义的选项还有一个显著的特点:它可以接收参数，`fn`也可以工作，但是它忽略了`defaultValue`属性。文档中没有提到这一点，但是在[源代码](https://github.com/tj/commander.js/blob/82d0d0ae6d66163f5c97ce56031b796621bef4ce/index.js#L396)中可以看到，`--no`选项的`defaultValue`会被重写为`true`，忽略你设置的`defaultValue`。所以，我的建议是你不应该为一个`--no`选项定义一个参数。

您可能已经注意到，选项参数有两种不同的定义方式，即`[]`和`<>`。区别在于`[]`定义了一个可选参数，而`<>`定义了一个必需参数。你可以在命令行输入`node index.js -u`来体验一下。会有一个错误，说:

```
error: option `-u, --username <name>' argument missing 
```

这是因为`-u`选项有一个必需的参数。只要使用这个选项，就必须给它一个参数。否则将会出现错误。

> **【提示】**
> 
> *   使用需要参数的选项时，注意不要提供必需的参数。例如，当您运行`node index.js -u -a`时，`-a`选项不会被触发，因为您输入的“-a”会被识别为`-u`的参数。

`-g, --gender`选项有一个正则表达式作为它的`fn`，只匹配“男性”或“女性”。这意味着，当`-g`的参数既不是“男”也不是“女”时，它将落入默认值“私有”。

> **【提示】**
> 
> *   使用正则表达式时，请确保设置默认值。在上面的例子中，如果没有给定默认值，而你输入了一个除“男性”或“女性”之外的参数，`mycli.gender`的值将会是`true`，这可能是你没有预料到的。

`-i, --additional-info`选项有一个名为`collect`的处理功能，定义如下:

```
/**
 * This function is used for collecting values into the array.
 * @param {string} val The new value to be pushed into the array
 * @param {array} arr The original array
 * @return {array} The new array
 */
const collect = (val, arr) => {
  arr.push(val)
  return arr
} 
```

这个函数只是收集新值，并将其放入原始数组。结合默认值`[]`，该选项可以被多次调用，并将所有参数收集到一个数组中。

示例输入:

```
node index.js -i "the sun rises in the east" -i "the sun sets in the west" 
```

示例输出:

```
The bot says: Hello world // (username)
The bot says: I do not know your age // (age)
The bot says: Well, gender is your privacy // (gender)
The bot says: I also know the sun rises in the east // (additionalInfo)
The bot says: I also know the sun sets in the west // (additionalInfo) 
```

最后两行对应我们输入的两个句子。

如果我们不使用`collect`函数，将默认值设为`[]`，会发生什么？我们可以用`-u`来测试这一点。

示例输入:

```
node index.js -u Tom -u Mary -u Mike 
```

示例输出:

```
The bot says: Hello Mike // (name)
The bot says: I do not know your age // (age)
The bot says: Well, gender is your privacy // (gender) 
```

如你所见，最后一个`-u`选项覆盖了所有之前的`-u`选项。

> **【提示】**
> 
> *   如果最后一个`-u`选项没有参数，即使所有前面的`-u`选项都有给定的参数，也会出现 en 错误，因为它们已经被覆盖。

正如其描述所言，`-s, --silent`选项禁用所有输出，因为所有的`bot`函数(是一个包装的`console.log`)都依赖于`mycli.silent`为假。

`--no-gender-output`选项仅禁用性别线。

在我们进入下一步之前，我想提一下[指挥官](https://github.com/tj/commander.js)支持`-`旗帜的缩写。但是当你试图使用它的时候要小心！

示例输入:

```
node index.js -uagi Tom 18 male "Michael Jordan is the God of basketball." 
```

示例输出:

```
The bot says: Hello -a // (name)
The bot says: I do not know your age // (age)
The bot says: Well, gender is your privacy // (gender)
The bot says: I also know Tom // (additionalInfo) 
```

乍一看，您可能会发现输出相当奇怪。但是如果你知道它是如何工作的，你马上就会明白。

缩写的机制很简单。缩写的选项在被评估之前将被简单地展开。所以原始输入变成了:

```
node index.js -u -a -g -i Tom 18 male "Michael Jordan is the God of basketball." 
```

*   `-u`将“-a”作为其参数，因此输出的第一行是“Hello -a”
*   `-g`没有参数，所以使用默认值“private”。
*   `-i`取“Tom”作为其参数，其余参数被舍弃。

好了，现在你已经实现了一个简单的 CLI 工具，也了解了一些表面背后的机制。恭喜你！让我们进入下一步。

## 步骤 03:添加子命令

CLI 工具通常有多个命令。在这一步中，我们将向 CLI 工具添加一些子命令。

```
git checkout step-03-add-subcommands 
```

或者手动修改您的`index.js`:

```
 // index.js

// ...

mycli
  .command('time')
  .alias('t')
  .description('show the current local time')
  .action(() => {
    /**
     * The `Date.now()` method returns the number of milliseconds elapsed since January 1, 1970 00:00:00 UTC.
     * By using `new Date()`, a Date object is created.
     * The `.toLocaleTimeString()` method then transforms it into the human readable form.
     */
    const now = new Date(Date.now())
    console.log(now.toLocaleTimeString())
  })

mycli
  .command('sum')
  .alias('s')
  .arguments('<numbers...>')
  .description('calculate sum of several numbers')
  .action(numbers => {
    /**
     * `Array.prototype.reduce()` executes the reducer function on each member of the array,
     * resulting in a single output value.
     */
    console.log(
      numbers.reduce(
        (accumulator, currentValue) =>
          parseFloat(accumulator) + parseFloat(currentValue),
      ),
    )
  })

mycli
  .command('match')
  .alias('m')
  .arguments('<first> <second> [coefficient]')
  .option('-r, --random', 'add a random value to the final result')
  .description('calculate how much the first person matches the second one')
  .action((first, second, coefficient = 1, cmd) => {
    let result = Math.abs(first.length - second.length)
    if (cmd.random) {
      result += Math.random()
    }
    result *= coefficient
    console.log(`The match point of ${first} and ${second} is ${result}`)
  })

/**
 * This line is necessary for the command to take effect.
 */
mycli.parse(process.argv) 
```

我们添加三个命令，分别是`time`、`sum`和`match`。

首先，让我们看看我们的帮助信息。

```
node index.js -h 
```

输出应该是:

```
Usage: index [options] [command]

Options:
  -u, --username <name>                             specify the user's name
  -a, --age [age]                                   specify the user's age
  -g, --gender [gender]                             specify the user's gender (default: "private")
  -i, --additional-info [info]                      additional information (default: [])
  -s, --silent                                      disable output
  --no-gender-output                                disable gender output
  -h, --help                                        output usage information

Commands:
  time|t                                            show the current local time
  sum|s <numbers...>                                calculate sum of several numbers
  match|m [options] <first> <second> [coefficient]  calculate how much the first person matches the second one 
```

[指挥官](https://github.com/tj/commander.js)也为子命令生成帮助信息。例如:

```
node index.js match -h 
```

将产生:

```
Usage: match|m [options] <first> <second> [coefficient]

calculate how much the first person matches the second one

Options:
  -r, --random  add a random value to the final result
  -h, --help    output usage information 
```

定义子命令很容易:

1.  `.command()`指定子命令的名称
2.  `.alias()`指定子命令的别名
3.  `.description()`指定显示在帮助消息中的描述。
4.  定义子命令将接受什么参数
5.  `.action()`定义子命令被触发后的动作

`time`命令没有参数，所以我们只需:

```
node index.js time

# Or `node index.js t`
# For it has the alias "t" 
```

将打印当前时间，例如:

```
11:02:41 PM 
```

`sum`命令需要至少一个参数。这是通过`.arguments('<numbers...>')`实现的。就像我们在步骤 02 中所熟悉的一样，这里的`<>`意味着这个参数是必需的。那`...`是什么意思？这意味着可以有多个参数。

让我们试一试:

```
node index.js sum 1 2 3 4 5.1 
```

输出将是:

```
15.1 
```

如上所示，`sum`命令接受我们输入的所有五个数字。这些数字被加载到一个名为`numbers`的数组中，我们可以在`.action()`的上下文中直接使用它。

`match`命令有两个必需参数`<first>`和`<second>`，以及一个可选参数`coefficient`。它还有一个选项`-r, --random`。

让我们试一试:

```
node index.js match Tom Mary 1.2 -r 
```

示例输出(结果会有所不同，因为我们在这里使用了随机数):

```
The match point of Tom and Mary is 2.0254795433768233 
```

`.arguments`部分不难理解。然而，`.action()`部分确实需要你的注意，因为有些东西与我们已经知道的不同。

我已经复制了下面的代码，所以你不需要向上滚动。

```
.action((first, second, coefficient = 1, cmd) => {
    let result = Math.abs(first.length - second.length)
    if (cmd.random) {
      result += Math.random()
    }
    result *= coefficient
    console.log(`The match point of ${first} and ${second} is ${result}`)
  }) 
```

`coefficient`是一个可选参数，为了避免出现`undefined`的情况，我们给它赋了一个默认值。

与我们在步骤 02 中所做的不同，由于这是子命令的上下文，我们不能直接使用`mycli.xxx`。相反，我们将`cmd`传递给函数，并使用`cmd.random`来获取`-r, --random`选项的值。除此之外，你可以用同样的方式使用选项。

## 步骤 04:使用 [shelljs](https://github.com/shelljs/shelljs)

直到现在，我们的 CLI 工具还只是一个玩具。在这一步，我们将通过使用 [shelljs](https://github.com/shelljs/shelljs) 让它变得更有用，如果你想在 NodeJS 中运行 shell 命令，这是非常有用的。你当然可以不使用它，但是你必须处理像输出的后期处理这样的事情。

```
git checkout step-04-use-shelljs 
```

或者手动修改您的`index.js`:

```
 // index.js

const mycli = require('commander')
const shelljs = require('shelljs')

// ...

mycli
  .command('shell')
  .description('use shelljs to do some shell work')
  .action(() => {
    shelljs.ls('-Al').forEach(file => {
      const birthTimeUTC = new Date(file.birthtimeMs).toUTCString()
      console.log(`${file.name} was created at ${birthTimeUTC}.`)
    })
  })

/**
 * This line is necessary for the command to take effect.
 */
mycli.parse(process.argv) 
```

添加了一个名为`shell`的新子命令。使用带有`-Al`选项的`shelljs.ls()`，这个子命令可以列出当前目录中的所有文件和目录，并分别告诉我们它们的创建时间。

```
node index.js shell 
```

示例输出:

```
.git was created at Thu, 03 Jan 2019 10:09:05 GMT.
.gitignore was created at Thu, 03 Jan 2019 10:09:13 GMT.
.lintstagedrc was created at Thu, 03 Jan 2019 11:36:11 GMT.
.prettierrc was created at Thu, 03 Jan 2019 11:36:11 GMT.
LICENSE was created at Thu, 03 Jan 2019 10:09:13 GMT.
README.md was created at Thu, 03 Jan 2019 10:09:13 GMT.
index.js was created at Fri, 04 Jan 2019 15:17:22 GMT.
node_modules was created at Thu, 03 Jan 2019 10:11:06 GMT.
package.json was created at Thu, 03 Jan 2019 11:36:11 GMT.
yarn.lock was created at Thu, 03 Jan 2019 11:36:11 GMT. 
```

shelljs 的详细用法可以在它的[文档](http://documentup.com/shelljs/shelljs)中找到。

> **【提示】**
> 
> *   还有一个包叫做 [shell.js](http://shelljs.io) ，用来在网页中渲染一个交互 shell。不要和[炮弹](https://github.com/shelljs/shelljs)搞混了。

## 步骤 05:重构目录结构

我们的代码现在有点脏。让我们通过重构让它变得更漂亮。

这次推荐 Git checkout，因为有很多修改。

```
git checkout step-05-refactor 
```

让我们来看看我们的新`index.js`:

```
// index.js

/**
 * This is the common way to import a package in NodeJS.
 * The CommonJS module system is used.
 */

const mycli = require('commander')
const mainAction = require('./src/actions/index')
const timeAction = require('./src/actions/time')
const sumAction = require('./src/actions/sum')
const matchAction = require('./src/actions/match')
const shellAction = require('./src/actions/shell')
const collect = require('./src/helpers/collect')
const {version} = require('./package')

/**
 * Without using `.command`, this works as the root command.
 */
mycli
  .version(version, '-v, --version')
  .option('-u, --username <name>', `specify the user's name`)
  .option('-a, --age [age]', `specify the user's age`)
  .option(
    '-g, --gender [gender]',
    `specify the user's gender`,
    /^(male|female)$/i,
    'private',
  )
  .option('-i, --additional-info [info]', 'additional information', collect, [])
  .option('-s, --silent', 'disable output')
  .option('--no-gender-output', 'disable gender output')

mycli
  .command('time')
  .alias('t')
  .description('show the current local time')
  .action(timeAction)

mycli
  .command('sum')
  .alias('s')
  .arguments('<numbers...>')
  .description('calculate sum of several numbers')
  .action(sumAction)

mycli
  .command('match')
  .alias('m')
  .arguments('<first> <second> [coefficient]')
  .option('-r, --random', 'add a random value to the final result')
  .description('calculate how much the first person matches the second one')
  .action(matchAction)

mycli
  .command('shell')
  .description('use shelljs to do some shell work')
  .action(shellAction)

/**
 * Other commands will be redirected to the help message.
 */
mycli
  .command('*')
  .action(() => mycli.help())

/**
 * This line is necessary for the command to take effect.
 */
mycli.parse(process.argv)

/**
 * Call `mainAction` only when no command is specified.
 */
if (mycli.args.length === 0) mainAction(mycli) 
```

正如您所看到的，所有的动作都被移动到目录`./src/actions`中，而助手函数被移动到目录`./src/helpers`中。

我们从`package.json`中读取`version`，并使用`.version()`来定义我们 CLI 工具的版本。现在您可以输入`node index.js -v`，输出将是:

```
1.0.0 
```

这是在我们的`package.json`中定义的

另一个修改是`*`子命令。通过使用通配符，它可以匹配与上述子命令都不匹配的所有其他子命令。然后我们通过使用内部`mycli.help()`将它们重定向到帮助消息。

我们在最后处理根命令，甚至在`mycli.parse`之后。为什么？

我们忘记了在步骤 03 和步骤 04 中测试 root 命令的可用性。现在回过头来试一试，你会发现`node index.js -u Tom -a 18`并不会提供我们期望的输出，除非你加上别的东西，比如说`node index.js hello -u Tom -a 18`。

> **【提示】**
> 
> *   当指定子命令时，根命令的`.action()`将表现为属于一个`*`子命令。
> *   如果`*`子命令也被定义，根命令的`.action()`将被忽略。

所以我们把主动作的执行移到了`index.js`的末尾，在`mycli.parse()`被调用之后。

那我们为什么需要`mycli.args.length === 0`条件呢？您可以删除它，您会发现即使我们使用其他子命令，主操作也会被执行！这绝对不是我们想要的。通过使用`mycli.args.length === 0`，主动作只有在没有子命令时才会生效。

> **【提示】**
> 
> *   您可能想知道如果 root 命令需要一个参数会发生什么。
> *   答案是不会生效，因为它会被认为是一个子命令，而不是根命令的一个自变量。

## 步骤 06:通过 [pkg](https://github.com/zeit/pkg) 封装 CLI

最后一部分，我们将把 CLI 打包成一个可执行的二进制文件。在 [pkg](https://github.com/zeit/pkg) 的帮助下，将 NodeJS 项目打包成不同平台的二进制文件相当容易。

```
git checkout step-06-package 
```

几个脚本已添加到`package.json`:

```
"scripts":  {  "package:mac":  "pkg mycli.js --targets node10-macos-x64 --output dist/mac/mycli",  "package:win":  "pkg mycli.js --targets node10-win-x64 --output dist/win/mycli.exe",  "package:linux":  "pkg mycli.js --targets node10-linux-x64 --output dist/linux/mycli",  "package:all":  "yarn package:mac && yarn package:win && yarn package:linux"  } 
```

它们用于为不同的 NodeJS 版本、平台和架构打包我们的 CLI 工具。

> **【提示】**
> 
> *   您可能已经注意到`index.js`已经被重命名为`mycli.js`，这是为了使帮助消息的输出与我们期望的名称相匹配。它使用主 JS 文件的名称作为 CLI 工具的名称。

现在，尝试为您的平台打包一个二进制文件，并尝试打包的二进制文件。最令人兴奋的是，这个二进制甚至独立于`node`！

## 步骤 07:将您的 CLI 工具发布到 NPM

```
git checkout step-07-publish 
```

这次，对`package.json`进行了更改:

```
 "name":  "@pkuosa-gabriel/node-cli-starter",  "bin":  {  "mycli":  "./mycli.js"  }, 
```

有两个关键点:

1.  将`name`属性重命名为“@organization/package-name”形式。
2.  添加`bin`属性来指定这个包的二进制文件。

另外，不要忘记在`mycli.js`的开头添加以下一行:

```
#!/usr/bin/env node 
```

以便系统知道用`node`执行`mycli.js`。

要发布包，您需要注册一个帐户，创建一个组织，然后在本地登录。完成所有操作后，只需运行:

```
yarn publish
# Or `npm publish` 
```

你的包裹很快就会寄到 NPM。

然后您可以运行`yarn global add @organization/package-name`，然后您应该能够在命令行中使用`mycli`。万岁！

本教程到此结束。感谢您的阅读！

* * *

## 步骤 0x【可选】:通过[温斯顿](https://github.com/winstonjs/winston)添加日志

如果您想进一步改进您的 CLI 工具，让日志更有条理是一个明智的想法。这里，我们将使用 [winston](https://github.com/winstonjs/winston) 作为我们的日志框架。想要一些颜色吗？你可以用[粉笔](https://github.com/chalk/chalk)。