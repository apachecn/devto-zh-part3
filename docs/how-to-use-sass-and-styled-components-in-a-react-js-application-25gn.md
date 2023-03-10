# 如何在 React JS 应用程序中使用 Sass 和样式化组件

> 原文：<https://dev.to/iam_timsmith/how-to-use-sass-and-styled-components-in-a-react-js-application-25gn>

今天，我们将介绍一些流行的方法来设计 React JS 应用程序的样式，比如设计组件和 Sass，来解释这两种方法的优点。

关于人们是否应该使用 CSS-in-JS 或 CSS-in-Javascript，在线开发社区正在进行一场激烈的争论。这篇文章不是同意或不同意这些观点，而是给你一个介绍，这样你就可以自己决定了。

我们今天要介绍的两个工具是 Styled Components，它是一个 CSS-in-JS 库，可以很容易地在 React js 项目中实现。另一个工具叫做 Sass，它是过去几年 javascript 世界中流行的工具。

对于每个示例，我们将创建一个包含图像、用户名、时间戳、用户照片和状态的社交卡组件。还应该注意的是，这些并不是 React 特定的。你可以在 Vuejs、Angular 或者任何你用 javascript 构建的东西中使用它们。

## 为什么人们会为 CSS-in-JS 争论不休？

争论的主要原因与关注点的分离有关。将我们的风格写在组件内部是否更好？我们应该把我们的风格和 javascript 分开吗？

我不知道一定有一个“正确”的答案。我确信存在每种情况都更合适的用例，尽管我发现自己更经常使用 Sass。让我们更深入地了解每一项，这样您就可以决定自己更喜欢哪一项了！

## CSS-in-JS

CSS-in-JS 的概念在 2014 年被讨论时开始引起轩然大波。从那以后，许多不同的库被创建出来，试图将这个概念变成现实。其中一些库是:[风格组件](https://www.styled-components.com/)、[镭](https://formidable.com/open-source/radium/)、[阿芙罗狄蒂](https://github.com/Khan/aphrodite)或[情感](https://emotion.sh/docs/introduction)。

在这篇文章中，我将使用样式化的组件，尽管我建议检查一下每一个，因为每一个都有自己的语法和工作方式。每一个的文档对于学习基础知识和了解哪一个感觉最舒服都是很好的。

### 安装样式组件

假设我们已经建立了一个 React js 项目，我们可以通过在终端中运行`npm install styled-components`或`yarn add styled-components`来将样式化的组件添加到项目中。这将向项目添加依赖项，并让我们准备好应用程序的样式。

### 创建带有样式组件的卡片

下一步将是[创建一个组件](https://www.iamtimsmith.com/blog/how-to-create-a-component/)来显示我们的卡片。我们可以使用下面的代码来做到这一点:

[![Set up Card in styled components](img/e8bd98f939c1c12e448c02851eb2a334.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v7BW8gp6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m2yarp1qp8wx1p84p381.png)

先说上面的代码。如果你熟悉 CSS，你可能会看到一些看起来很熟悉的东西，也会看到一些看起来有点奇怪的东西。Styled Components 允许我们像在 CSS 文件中一样编写样式，尽管其他一些 CSS-in-JS 库会要求我们使用 Camel 大小写，因为它实际上只是创建一个 javascript 对象。

上面的代码所做的是创建一个名为 Card 的`const`变量，它告诉`styled`应该创建一个具有以下属性的 div。任何 DOM 元素都可以这样做，所以如果你想设计一个 h1 标签，你可以使用`styled.h1`。

### 添加媒体查询和嵌套

在 Card 变量中，我们还可以加入媒体查询，以使事情变得更有响应性。如果我们添加一个类似下面代码的媒体查询，你可以看到当我们达到 1000 像素的最大宽度时，卡片背景变成红色。这不是我们实际想要添加的样式，它只是用于演示目的。

[![Adding a media query to a styled component](img/fa40fe5cfe7aae4ad58ff19d81c82034.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jw1NggnE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/koghjyarcymi744778iu.png)

我们还可以将样式嵌套在样式组件中，这简化了我们正在编写的代码。如果你使用过 sass 或更少，你已经熟悉这个概念。下面的例子展示了我们如何在 card 组件中处理图像的样式:

[![Nesting selectors in styled component](img/c46b7c56c99363f265022ceb4cd5307b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3HenLGsE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m11onqm7gwfvugdgalh8.png)

### 在样式中使用 Javascript

使用 CSS-in-JS 的另一个好处是我们可以混合 javascript 和样式。我意识到这听起来很明显，但是我的意思是我们可以使用逻辑来显示事物，我们可以将变量传递到我们的样式中。有了 React js 这样的库，这可以成为一个非常强大的工具。我们不会保持这种风格，因为它有点讨厌，但为了说明的目的，我们可以用字符串文字这样做:

[![Using a javascript variable in a styled component](img/4bd9c646aa10a83a2f15e8d694243b12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b22RzeGr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bl821n3duy8x3h1kuaw3.png)

使用 CSS-in-JS 允许我们保持局部的风格，而不是所有的都是全局的，或者必须添加大量的类来使它工作。这意味着事情可以更简洁，我们不必担心一种风格会覆盖另一种风格。此外，这意味着如果我们需要改变风格，我们总是知道去哪里找。我们不必在一堆 CSS 文件中寻找一个类。

为此，我们应该从 styled-components 导入 ThemeProvider 组件，并提供一个主题变量。主题变量将包含我们希望在样式中使用的任何全局变量。例如，如果你在导航条、按钮和链接中使用了一种主要颜色，那么在每次你想使用它的时候硬编码是没有意义的。

### 使用全局主题

万一哪天要更新呢？这听起来像是大量的搜索和替换在等待发生。相反，我们可以声明全局主题变量，并从任何样式化的组件中访问它。这样做非常简单，如下图所示。

[![Using a global theme variable](img/180871c51907ca700ac2a731e1471151.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EUVx2Uf4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/acuqixckn5rappjq9l4i.png)

这些主题变量可以在我们的任何组件中使用，因为 ThemeProvider 包装在我们的整个应用程序中。然后，我们可以添加一个样式组件来包装我们的站点，该组件将处理所有的全局样式，如排版、输入样式和其他应该在所有页面上标准化的内容。

为了演示这一点，让我们为我们的社交卡添加一些全局样式和一些内容。下面的代码向社交卡片添加了必要的内容，还添加了一些应用于文本的全局样式。

[![Completed social card using styled components](img/86067af9c58728c13a20c0b7fb59bf2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lKaBV35J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cc8b1pe05rd9xtkcp5ud.png)

这显然是一个非常基本的例子，但这是一个让我们接触样式化组件的简单方法。我们还可以用它做一些更深入的事情，这些我们可以在文档中了解到。

样式组件非常好，因为它允许我们将样式放在同一个文件中，而不是打开几个不同的文件来寻找正确的样式。这是我们完成的带有风格化组件的社交卡的外观:

[![Finished social card with Styled Components](img/d65e98575884f02991852d6ef38d70c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hRwkpQvJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nmqj4o6ytzofvucrhp8h.png)

## 在 React 应用程序中使用 Sass

Sass 是几年前创建的，最初是在使用 Ruby 构建的应用程序中实现的。近年来，它已经适应了 Node.js，这就是我们今天使用它的方式。你可能会注意到我们正在创建“scss”文件，但称之为 *sass* 。这是因为 Sass 最初是用某种语法创建的，这种语法实际上被称为“Sass”。后来，一种更类似 CSS 的替代语法被创造出来，这就是所谓的“Scss”。因为 Scss 具有与 Sass 相同的功能，所以它仍然属于 Sass 的范畴。

在 React js 应用程序中启动并运行 Sass 通常非常简单，尽管如何捆绑或编译应用程序会有所不同。在本教程的代码中，我将[使用 Parcel js，它很容易启动和运行](https://www.iamtimsmith.com/blog/parcel-js-who-says-bundling-needs-to-be-difficult/)并为我们处理 Sass。还有其他一些有时是必要的库，比如 [node-sass](https://github.com/sass/node-sass) 、 [gatsby-plugin-sass](https://www.gatsbyjs.org/packages/gatsby-plugin-sass/) ，或者 [next-sass](https://github.com/zeit/next-plugins/tree/master/packages/next-sass) 。

### 设置我们的 Sass 文件

在 React js 应用程序中使用 Sass 有几种不同的方式。第一种方法是在一个文件夹中创建一个组件，并将该组件的样式作为一个`.scss`文件包含在该文件夹中，并将它们直接导入到组件中。我已经这样做了，并发现这很容易，但我不太喜欢这个组织。

另一种方法是在我们的项目中创建一个 Sass 文件夹，这就是我们的风格所在的地方。这是我们今天将采用的组织方法。也就是说，我们将在项目中创建一个名为“Sass”的文件夹，并添加一个名为“app.scss”的文件。虽然我们可以将所有的样式放入这个 app.scss 文件中，但这会变得混乱，并且不会提供比普通 css 更多的好处。

相反，我们将创建单独的文件，并将它们导入 app.scss 文件。然后，我们可以将 app.scss 文件导入到 app.js 文件中，剩下的工作由 Parcel 来完成。

### Sass 文件夹的结构

关于如何用我们的风格来组织文件夹，有很多不同的观点。我们可以花大量的时间去研究组织技术，但是我发现我喜欢的方法是把我的 Sass 文件夹和我的项目组织在一起。通常，这将转化为以下布局:

*   /Sass
    *   /Components -包含每个 React 组件的. scss 文件的目录
    *   /Pages -对于需要自定义样式的每个页面都有一个. scss 文件的目录
    *   /Templates(可选)-如果使用模板(对于 gatsby 之类的工具)的话，模板的目录
    *   / `_elements.scss` -站点的任何通用样式。选择器不应该有任何类或 id。
    *   / `_keyframes.scss`(可选)-我将用于该网站的任何关键帧或动画。
    *   / `_mixins.scss` -任何将被反复使用的混合(样式片段)
    *   / `_variables.scss` -将在整个样式中使用的任何变量
    *   / `app.scss` -导入所有其他 scss 文件的文件

您可能注意到的关于文件名的第一件事是，其中一些文件名以下划线开头。这是因为在 node 之外，Sass 实际上被编译成一个 CSS 文件。任何开头没有下划线的 SCSS 文件都会被编译为不同的样式表。因为我们将所有的样式表都放入 app.scss 文件中，而不是将它们分开，所以它们都应该以下划线开头。

因为我们的应用程序只有一张社交媒体卡，所以我们不需要所有这些。为了简单起见，我们将使用 app.scss、_variables.scss 和 _elements.scss 文件以及组件目录。

话虽如此，我们还是开始吧！

### 在 Sass 中设置变量

因为我们的样式组件样式中有一些变量，所以我们也可以在这里设置它们。为此，在 Sass 文件中为我们的样式创建一个名为`_variables.scss`的文件。在`_variables.scss`文件中，添加以下代码。

[![Adding variables to the _variables.scss file](img/95e3b7bd78484cfea65807a955e5ad65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JnZCnaRg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m0q6jhhytrne5n0jt5r2.png)

一旦我们添加了一些变量，我们应该将其导入到`app.scss`文件中。因为我们希望在样式中的任何地方使用这些变量，所以这个导入应该放在导入列表的顶部。下面的代码行将变量添加到我们的样式中。我们还可以看到，我们不必在 import 语句中包含下划线或文件扩展名，因为 Sass 足够聪明，知道我们的意思。

[![Adding variables to app.scss](img/7e01d1e42304881f680acff417623c5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ri7CcUOc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dfwotsvhzbgp33odj261.png)

### 为元素创建全局样式

我们为 styled components 应用程序创建了一些全局样式，将我们的字体设置为 sans-serif 字体，并将我们的文本颜色设置为`#333`。让我们继续在 Sass 中创建这些样式。首先，我们将在 Sass 文件夹中创建一个`_elements.scss`文件。下面是我在这个文件中使用的样式，你会注意到我使用了我们创建的变量，就像我们使用样式组件一样。

[![Creating a global style](img/0bbb7a4ab8d37b143bca5b50fa6afe05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GFBXE4sQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6dwuuzwyqww4gqfi16rn.png)

现在我们可以将`_elements.scss`文件导入到 app.scss 文件中。看看能不能不看。我们的 app.scss 文件现在应该如下所示:

[![Import styles into app.scss](img/a84adc8af169ac389592e0ded5d50ad3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UJhejYtc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xjzkdcuvd18k9e262x0k.png)

### 向组件中的元素添加类

因此，我们可以将 React js 组件中的元素作为目标，我们应该添加一些类和/或 id。像 Boostrap 和 Foundation 这样的 CSS 框架需要大量的类，这些类可能会变得相当混乱。我最喜欢的代码是在样式元素上没有 30 多个类的代码，所以记住这一点，让我们继续用下面的代码向我们的组件添加一些类:

[![Adding classes to our component](img/0308f45a47b64799611470949e97c962.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TfH-3YRe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v36zat7oac8l05s5iv5p.png)

厉害！让我们开始设计我们的组件吧！在 Sass 文件夹中，创建一个名为“components”的新文件夹。这个文件夹将保存所有 React js 组件的样式。通常，我会将每个组件放在它自己的文件中，这是一个非常简单的应用程序，所以我们将把它保存在 app.js 文件中。

我们可以像设置样式组件一样设置样式，因为 Sass 也可以处理嵌套和媒体查询。因为我们的 import 语句将在 app.scss 中的变量导入之后出现，所以我们可以使用在此文件之前导入的任何内容。这对于 mixinss 很有用，mixin 是可以重用的代码片段。您甚至可以创建一种函数，在其中传递一个参数，并在 mixin 代码中使用它。

下面的代码将社会卡的样式设置为与我们的样式组件社会卡相同:

[![Social card styles in Sass](img/1f76f7117e965eea753c17a28d07775e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qbjkY0N0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c6aov63hd2i0sxlefb3y.png)

现在，我们可以在 app.scss 文件中编写导入语句，这样我们就可以为卡片引入样式了。

[![Import social card styles in app.scss](img/4d032b1a1cd8fa00ac587dbd4b5f7361.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6w_eqlch--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1qoethohrhxjsrscoe6.png)

就这样，这是我们 sass 风格的最后一个社交卡组件。它看起来与样式化组件中的组件一样。

[![Final Social Card with Sass styles](img/31bbf37f5312aefb7f17546394ba9819.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wVUKPUcK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zwik689ilxqe3u7x0c7q.png)

## 关于 Sass vs Styled 组件的最终想法

如您所见，Sass 和 Styled 组件都是 React js 应用程序中增强样式的有价值的方式。最后，我认为你应该用你最喜欢用的那个。我发现自己更经常使用 Sass，但是我也喜欢在我的一些个人项目中使用 CSS-in-JS。如果你想看看这篇文章中提到的代码或者玩玩这些代码，[可以在这里找到](https://github.com/iamtimsmith/styled-components-vs-sass)。