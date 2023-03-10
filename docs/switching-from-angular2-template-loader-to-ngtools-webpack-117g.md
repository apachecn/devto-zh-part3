# 从 angular2-template-loader 切换到@ngtools/webpack

> 原文：<https://dev.to/antjanus/switching-from-angular2-template-loader-to-ngtools-webpack-117g>

我通常不会写“实用”的帖子来描述解决一个非常具体的问题的方法，但我在网上遇到了一些似乎没有好的解决途径的事情。

这篇文章的一大部分是我为了让一切正常工作而做的调试/故障排除。整个项目花了我一个多星期的时间专注于工作，并且几乎每天都让另一个编码员进行几个小时的配对。我不得不从我的笔记本电脑(它在不断的重新编译中变得过热和断断续续)切换到我的台式机，目前我的办公室已经超过了舒适的温度。这是一个痛苦的过程，但是...我做到了！

## 问题

如果你正在使用 Angular(或“Angular2+”)，你可能会使用 [angular2-template-loader](https://github.com/TheLarkInn/angular2-template-loader) ，它允许你做一些整洁的事情，例如`require`你所有的组件模板和 SASS 文件，然后你可以通过 Webpack 中的其他加载器运行它们。

您将为您的组件发送类似这样的内容:

```
@Component({
  template: require('./button.component.html'),
  styles: [require('./button.component.scss')]
})
export default ButtonComponent {} 
```

Enter fullscreen mode Exit fullscreen mode

哇，看看这个，我们可以在角分量中使用 SCSS。这就是它的力量。Angular2-template-loader 将加载 html/scss(或者更确切地说是它的后处理版本),并将它们内联到模板本身中。

问题是这实际上不允许 AoT 或提前编译。因此，尽管 angular2-template-loader 非常流行，经常在教程中使用，并且非常容易设置，但它也为 AoT 编译器创建了一个有问题的解决方案。

## AoT 编译器

AoT 代表“提前”。AOT 编译器将查看组件中引用的模板，解析它们，并创建 JavaScript 逻辑来完成模板要求它做的事情。

对我来说，描述 AOT 编译器的最好方式是，不要解析 HTML，找出哪里有重复，哪里引用了什么组件，等等。在应用程序引导时(当浏览器加载应用程序时)，它发生在构建时。

在我们的工作中，这个过程似乎需要几秒钟，这是绝对荒谬的。另外，如果您在应用程序加载时进行编译，那么您必须将编译器包含在您的包中。:(

但是...angular2-template-loader 不做 AOT(它没有声称它做！)而 AOT 不能与之发生关系。

## web pack 加载器替代

我发现的直接替代方案是 [@ngtools/webpack](https://www.npmjs.com/package/@ngtools/webpack) 包，它不仅可以 AOT，还可以作为一个类型脚本加载器！它还做了一些其他的事情，但我想先集中在这一点上。

首先，我们必须更换旧的`angular2-template-loader`和你正在使用的任何`typescript`加载器，最后看起来应该是这样的(你的打字稿加载器和你的 angular2-template-loader 应该已经不在了):

```
// somewhere in your webpack config
rules: [
  {
    test: /(?:\.ngfactory\.js|\.ngstyle\.js|\.ts)$/,
    loaders: [
       {
       loader: '@ngtools/webpack',
       },
    ],

  }
] 
```

Enter fullscreen mode Exit fullscreen mode

你还需要你的 webpack 插件:

```
import { AngularCompilerPlugin } from '@ngtools/webpack';

// and somewhere in your webpack config
plugins: [
    new AngularCompilerPlugin({
      tsConfigPath: './tsconfig.json',
      entryModule: './web/modern/main.ts#AppModule'
    }),
] 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。唯一的问题？所有这些都需要..

## 修复 html 需要

现在的问题是，我们有大量的组件对模板和样式很感兴趣。我们现在要干嘛？

这就是我写这篇文章的原因，这样我就可以分享/记录这些信息，以后再来看。唷。因此，`@ngtools/webpack`允许我们做类似的要求，但是没有`require`。

基本上，我们必须改变

```
@Component({
  selector: 'button-component',
  template: require('./button.component.html')
}) 
```

Enter fullscreen mode Exit fullscreen mode

成:

```
@Component({
  selector: 'button-component',
  templateUrl: './button.component.html'
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们仍然引用一个`templateUrl`，而不是一个`template`。NgTools 带来了那个模板，并在上面做了 AOT。那么我们如何在大范围内实现这种改变呢？通过使用 Unix 工具`grep`然后使用普通 ol' node 来做修改

## 清除所有文件

`Grep`是 Linux、macOS 和其他系统中的一个 unix 工具。也可以在 Windows 上获得。不幸的是，Windows 的`Select-String`不会做我们今天需要它做的工作(尽管[去阅读如何使用 Powershell 的 Select-String，如 grep](https://antjanus.com/blog/web-development-tutorials/how-to-grep-in-powershell/) ，如果你喜欢的话)。

使用`grep`我们可以选择所有需要更新的文件。

```
grep -rl 'Component({' /path/to/application 
```

Enter fullscreen mode Exit fullscreen mode

标志`-r`将确保 grep 递归查找应用程序中包含字符串`Component({`的文件。

如果你只是单独运行它，你会得到一长串需要更新的文件；但是，我们可以使用`node`来代替我们。

## 改变你的文件

事情是这样的，我试过`sed`，真的试过。但是做一个小操作花了这么长时间，我想我还不如为它写一个`node`脚本。

首先，我们需要一个非常复杂的正则表达式，它可以进行正确的替换，只用`./template.component.html` :
替换`require('./template.component.html')`

```
/Component\(\{(.*\n)+.*template: require\(('.*\.html')\)(.*\n)+\}\)/ 
```

Enter fullscreen mode Exit fullscreen mode

哦 s***，那是搞什么鬼？我不想这么说，但这是我们自由的通行证。这个正则表达式的作用是:

1.  寻找`Component({`字符串
2.  它匹配“用字符填充的任意多的新行”(这就是`(.*\n)+`)
3.  它找到所需的`template`字符串。注意多余的括号
4.  组匹配允许我们通过`('.*\.html')`来识别 HTML 字符串。
5.  然后我们匹配“无论有多少个换行符”
6.  最后我们为结束`})`比赛。

基本上任何符合这个模式的东西:

```
Component({
  something: else,
  doesnotmatter: whatshere,
  template: require('./path/to/template.html'),
  moreKeywords: withData
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个对象中有多少个键或者什么数据并不重要，只要它有一个需要 HTML 文件的模板，我们就会匹配它。

我们来写`node`剧本吧。它需要读取一个文件的路径作为参数，进行替换，然后将更改写入该文件。我将跳过一步一步解释的疯狂，所以这里是最终的 JavaScript 文件:

```
const path = require('path');
const fs = require('fs');

function run() {
  /*
   * Args which should look like ['node', 'path/to/this/script.js', '/path/tofile.ts']
   */
  const args = process.argv;
  const files = args.slice(2);

  files.forEach(file => {
    changeFile(file);
  });
}
function changeFile(relativeFilePath) {
  const filePath = path.resolve(__dirname, relativeFilePath);
  const fileString = fs.readFileSync(filePath).toString();
  const regex = /Component\(\{(.*\n)+.*template: require\(('.*\.html')\)(.*\n)+\}\)/;

  const match = fileString.match(regex);

  if (!match) {
    return;
  }

  const htmlPath = match[2];

  if (!htmlPath) {
    return;
  }

  const replacementLookup = `template: require(${htmlPath})`;
  const replacement = `templateUrl: ${htmlPath}`;
  const newFileString = fileString.replace(replacementLookup, replacement);

  fs.writeFileSync(filePath, newFileString);
}

run(); 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这个脚本将:

1.  读入脚本参数以获取文件路径
2.  一个一个地阅读文件
3.  使用那个奇特的正则表达式来查找匹配。匹配组 2(数组中的第三个项目)将是我们的 html url
4.  对文件字符串进行替换，用新值替换原来的模板值
5.  省省吧！

**注意**这个脚本非常方便。如果您在故障排除部分遇到该问题，您也可以使用它来更新`styles`。:)

## 把这一切联系在一起

好了，所以总的来说，我们多使用一个 unix 工具，`xargs`。它将把 grep 的结果通过管道传输到节点脚本中，然后节点脚本将执行我们的替换。

> **小心**这是一个破坏性的行动。这意味着它会影响每个文件并直接运行。我不对你在使用这些脚本时可能遇到的任何问题负责。确保你使用的是类似于`git`的东西，并且已经提交了到目前为止所有的代码变更，这样你就可以在遇到问题时做一个快速的`git hard --reset`,回到项目的工作版本。请确保这一点。

```
grep -rl 'Component({' /path/to/application | xargs node replace.js 
```

Enter fullscreen mode Exit fullscreen mode

脚本将每个文件作为一个单独的参数，所以 xargs 实际上调用了`node replace.js path/to/component1.ts path/to/component2.ts`等等。

在这之后，你应该完成了！

## 结果

我想分享一些我完成的初步基准测试:

1.  在未缓存的情况下，开发环境中的初始加载时间从 10 秒降至 3 秒
2.  在开发环境中，初始加载时间(缓存)从 8 秒降至 1.8 秒
3.  编译器时间是资源密集型的(我的办公室是一个桑拿室)

我迫不及待地想在生产环境中尝试一下。

## 模板问题？

编译器会指出你的问题。我已经在`Potential issues`中提到过它，但是首先值得单独说一下:angular-cli 将编译你的模板。这意味着它将检查变量，它将检查绑定，它将检查一切。如果你搞砸了，它会让 T2 告诉你。我必须解决的常见问题:

1.  在模板中引用组件中不存在的变量
2.  使用错误数量的参数调用函数
3.  将变量传递给没有接收该数据的输入的组件

## 潜在问题

在转换过程中，我遇到了一些问题，我想分享一下我是如何解决这些问题的。其中大部分已经开始和/或结束了`angular-cli`回购的发行。:/如果你直接查找错误，你就能找到它们，并跟上对话。我想给你提供我是如何解决这个问题的，以及其他的解决方案。

### *重要提示:*

1.  确保您已经保存了您的工作并提交到 Git 中——而不是提交到您的主分支。说真的。我不得不不断参考主分支和干净的代码库
2.  确保你不会试图提前改变事情。看看你遇到的潜在问题
3.  如果你不得不撤销一些解决方案，那也没关系
4.  我不能帮你解决这个问题

### 错误:无法确定/path/to/component 中类 AppRootComponent 的模块！请向 NgModule 添加 AppRootComponent 来修复它。

我在几期杂志的不同地方和其他地方看到了这一点。对于这个问题，我找到了四个解决方案:

#### 可能的解决方案 1

您可能有 webpack/node 不关心但编译器关心的错误导入路径。大多数时候，这是由于资本化。确保如果您正在导入一个有大写字母的文件(如`AppRootComponent.ts`)，您在导入路径中的大写字母是正确的。你其实可以做一个类似`./path/to/approotcomponent`的导入，node/webpack 也不会抱怨。

#### 可能的解决方案二

另一种可能性是，您的组件不是模块的一部分，或者只是未被导入但仍在工作目录中。检查这两种情况中的任何一种，将这些组件放入模块中或移除它们。

#### 可能的解决方案 3

最后，这是我的情况，你在你的主模块中使用组件。呃，我讨厌这个问题，因为我认为它不应该引起这些问题。基本上，如果你有一个`AppRootComponent`并使用它来引导 Angular，先把它放在另一个模块中，导入那个模块，*然后*引导。

#### 可能的解决方案 4

还有一个隐藏的错误。当我努力让一切正常工作并解决所有其他问题时，我发现我仍然能够在旅程结束时引导`AppRootComponent`,只要它是我的入口模块(AppModule)的一部分。因此...一旦发生这种情况，我最终恢复了上面的解决方案。

### path/to/component/button . component . ts . buttoncomponentcomponent . html 错误(1，21)::需要 0 个参数，但得到 1 个。

这通常意味着您的模板本身存在输入问题。在几种情况下会出现这种情况:

1.  您正在调用一个方法或使用一个在组件中被标记为`private`的变量
2.  你打字有问题。你把错误数量的参数传递给了一个方法(这就是我的情况！)

### 类型错误:无法读取未定义的属性“_ngToolsWebpackPluginInstance”

这个错误是我在使用 [HappyPack](https://github.com/amireh/happypack) 和`@ngtools/webpack`的时候出现的。它们不兼容，所以直接使用`@ngtools/webpack`而不是 HappyPack

### 模块未找到:错误:无法解析。/main.ts.ngfactory '在'/path/to/application/directory '中'

另一个我不明白的怪问题。请注意，`main.ts`是我的条目文件，因此名称可能会有所不同(例如`app.ts`)。对此，我找到了三种解决方案:

1.  调整 tsconfig [中的`rootDir`参数，查看相关问题](https://github.com/angular/angular-cli/issues/13721)
2.  安装增强版-通过`npm install enhanced-resolve@3.3.0` [解决相关问题](https://github.com/angular/angular-cli/issues/7151)
3.  您可以在 webpack 配置的`extensions.resolve`中将`.ngfactory`添加到 webpack 需要解析的扩展中

如果这些都不起作用，那么通过不指定`entryModule`并指定到`mainPath`的绝对路径来调整`@ngtools/webpack`配置。

```
{
  mainPath: path.resolve(__dirname, '/path/to/entry/file')
} 
```

Enter fullscreen mode Exit fullscreen mode

这禁用了懒惰的路由加载(按路由自动代码分割)，这是一个令人失望的，但在我的情况下是必要的，代码分割对我来说不是问题，因为我使用 Angular/AngularJS 混合应用程序，路由器位于 AngularJS 侧。

唷，我们继续走吧！

## 更复杂的构建链中的潜在问题

我想把这一部分分开，因为更有可能是我自己的特定应用架构导致了这些问题，而不是由`ngtools/webpack`负责的事情。

### 类型错误:library_1 .默认值未定义

这个问题基本上意味着模块不可用。这是一个问题，因为 TypeScript 编译器正在编译 JavaScript 文件。为什么这是个问题？

而巴贝尔对此没有异议:

```
import _ from 'lodash'; 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 需要以下语法:

```
import * as _ from 'lodash'; 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，这意味着完全切换到 TypeScript 编译器，并调整所有的导入路径——我去掉了我们的 Babel 编译器，并更改了`@ngtools/webpack`加载器以匹配所有的`.js`文件。

如果你想调整我上面提到的`replace.js`文件:`/import ([a-z0-9A-Z_]+) from/g;`，下面是查找正则表达式。我可以写一整篇关于这个过程的文章，所以如果你真的到了这里，就在下面评论吧！我会给你一步一步的过程。

但是，请确保不要更改您自己模块的导入路径！

### 主机不应该从`getSourceFile`返回重定向源文件

Github 上有一个关于这个错误的公开问题，它建议给编译器打补丁...是啊。补丁*起作用*(我已经试过了)，它基本上包括:

1.  抬头望`./node_modules/@angular/compiler-cli/src/transformers/program.js`
2.  查找以`if (this.hostAdapter.isSourceFile(sf.fileName)) {`开头的行
3.  把`if (sf['redirectInfo']) { sf = sf['redirectInfo'].redirectTarget; }`放在

这实际上是用重定向到的实际文件替换了源文件重定向。🤷‍♀️对我来说意义不大；然而，如果你不想手动修补 node_modules 中的东西(即使使用 Github 问题中包含的有用的`postinstall`脚本)，你可以做我所做的，而不是在步骤 3 中设置重定向，你可以注销有问题的文件！

```
if (sf['redirectInfo']) {
  console.log('SF: ', sf.fileName);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后解决出现的问题。在我的例子中，库`@turf/turf`有一些问题，也就是说，`@turf/turf`中的每个模块都在导入`@turf/helpers`和*，而*导致了一些问题。我能够通过以下方式解决这些问题:

1.  从有问题的库中析构任何导入
2.  直接使用/安装库的子模块

我也要把这个扔进去:如果对你来说不是一个巨大的花费，寻找替代品。

### 萨斯/SCSS 不装？

我也遇到了这个问题。有一张[关闭的票](https://github.com/angular/angular-cli/issues/6252)没有太多信息；然而，如果你想使用 SASS(以前没有！)，请查看该线程，了解有关 SASS 加载程序的信息。

然而，如果你以前使用过它，但现在不工作了，你只需要改变你加载样式的方式:

```
// from
@Component({
    styles: [require('./path/to/component.scss')]
})

// to
@Component({
    styleUrls: ['./path/to/component.scss'] 
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 附加资源

一路上有一些资源帮助了我:

*   将没有 cli 的项目升级到 Angular 6

## 但是为什么呢？

为什么我要分享这篇没有清晰路径的长篇文章？所以你能理解我的旅程。我是如何挣扎的，我看到了什么，我必须在哪里寻找答案。

今天我是一个新人。我看着镜子，看不到两周前我准备更换装载机时看到的安东宁。我不是同一个开发商。

这是艰难的时刻。我的理智受到了考验。我的毅力经受了考验。有很多个夜晚，我想对着 webpack，对着 Angular，对着 Angular-CLI 大喊。为此责备某人，但我不能。代码只是...有时候就是这样。有时，问题是不可重现的，因此开源维护者无法解决。然而，它成功了。成功了。

查看文章顶部的截图。看看我做了多少改变！它是...很多工作。

尽管如此...我很高兴`ngtools/webpack`的存在，以及`angular2-template-loader`在我刚开始过渡到 Angular 时的存在。如果没有 ngtools，我就无法将我们的加载时间减少到足以让我们进入应用程序开发的下一个阶段:重写 React 中的所有内容。