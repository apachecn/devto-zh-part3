# 为 Android Studio 编写 Java 解析器插件

> 原文：<https://dev.to/vitalipom/writing-androidstudio-java-parser-plugin-2b00>

# 编程-目标和后续步骤

在此第 2 篇第 10 炸文章(**注意，不会有什么好的！**)我将描述当今技术的另一个层面的设施，它允许我们在另一个层面上做**好**的事情。

我将为 java 编写语言解析器插件，以便允许在一个地方使用现有的设计模式实现进行多处编辑。(这句话的确没有道理。)

### 问题:

我们必须对一组值实现一个更改监听器，它将在资源重新加载后在 redraw() update 上依次更新视图。(这句话也没有道理。别担心，我正在努力。)

我正在为 Anysoftkeyboard 开发我自己的版本——一个免费授权的开源 Java Android 键盘。作为我工作的基调，我也在任何软键盘上工作，让它“更好”。

Anysoftkeyboard 作为一个独立的键盘，对于插件主题开发者来说有一个非常酷的功能，它允许用 xml 标记语言创建一个插件或主题，并让用户在他们的键盘上应用新的插件。创造一个好的键盘主题本身并不容易。

Anysoftkeyboard 代码库的问题是，它只允许修改新主题中的一些值，这些值不需要计算，如按钮边框、按钮颜色、键盘颜色和键盘字体。

**但是**

#### 如果你想用新的按钮高度创建一个新的主题，一旦加载了第一个主题，它就不起作用了——因为新的按键高度需要对所有的`y`位置进行新的计算。

我想要做的是为键盘中的每个键值创建一个更改监听器，这可能是键盘主主题 xml 中更改的主题。

等等。什么？

如果我有五个影响 25 个函数的值呢？我该如何更新它们？！

这正是我面临的问题，为了方便地查看所有使用我的值来设置其他值的地方，我计划编写一个语言解析器脚本。

Intellij 和 Android Studio 有这个 JFlex 特性，它基本上是一个 java 语言解析器，它本身是一个独立的语言，或者类似的东西。你可以在上面转储你自己的脚本，作为 Android Studio 或 Intellij 插件写在里面，瞧，你有东西了......非常酷。:)

基本上就是这样。

请分享您的想法，让我知道您是否有兴趣找到类似的文章，以及是否值得分享自动化语言解析脚本的过程和想法。我想知道我是否应该就这个话题再写一篇文章。)让我知道你是否会对这样的文章感兴趣。

谢谢，
vitali.pom

[![Language Interpreter in Intellij](img/f380095c43e99fc10ae902dca3106b18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V4IWCAy1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://confluence.jetbrains.com/download/attachments/49459812/generated_parser.png)