# 如何构建 Web 应用程序，第 2 部分

> 原文：<https://dev.to/gtanyware/how-to-build-a-web-app-part-2-5cln>

[< < <简介](https://dev.to/gtanyware/how-to-build-a-web-app-1-3fb2) | [故事>>](https://dev.to/gtanyware/how-to-build-a-web-app-part-3-5ae3)|[文字，文字，文字> > >](https://dev.to/gtanyware/how-to-build-a-web-app-part-4-1a52)

# 设置系统

在本系列的第二篇文章中，我将描述建立一个可以创建 web 应用程序的环境所需的步骤。如果你已经有了自己的公共网页，其中的大部分内容你都会很熟悉。和第一篇文章一样，我的目标读者是从初级到中级技术水平的人。

## 托管

当我在编码见面会上与朋友交谈时，发现他们中有多少人没有自己的网页，我总是感到困惑。他们努力在 PC/Mac 上设置 WAMP 或 MAMP，这样他们就可以开发 web 代码并在浏览器中运行，当我问“我能在智能手机上看到它吗？”他们没有办法做这件事。我的想法总是“为什么让生活如此艰难？”答案通常与托管的感知成本有关。

来吧伙计们。你带着闪亮的 MacBook Pro 来到这家咖啡店，刚刚支付了比一个好的托管计划的月成本更高的停车费、一杯脱脂拿铁咖啡和意式面包。你花了所有醒着的时间学习 React，这样你就可以成为一名专业的程序员，但你没有 URL 放在你的简历上，指向一个闪闪发光的演示，这会给潜在的雇主或客户留下深刻印象？这不算。

所以我的第一条建议是和一家好的托管公司签约。小心不要买最便宜的；这些可能有可靠性问题或对主机容量和开发者友好性的限制。基于十多年的客户经验，我的建议是 [DreamHost](https://dreamhost.com) ,因为他们对你的主机操作没有什么限制，而且他们的控制面板是众所周知的最友好的。

接下来就是获得自己的域名。你的托管公司可以为你做这件事，但我更喜欢把事情分开，用最好的，在这种情况下就是听起来不太可能的 [iwantmyname](https://iwantmyname.com) 。从大量可用的选择中找到一个好名字，设置 DNS 指向你的主机(DreamHost 在他们的列表中)，几分钟后你就上线了。

最后——构建 sparky 演示！

## 平台

平台是您用来放置代码的环境。你可以选择完全没有平台——这将适合那些想要从头开始学习，喜欢用键盘而不是鼠标做任何事情的铁杆传统主义者——或者某种内容管理器，其中最常见的是 WordPress。我将简要描述这两个选项，但由于 WordPress 使生活变得如此简单，这就是我从那时起的假设。

### 选项 1 -无平台

没有平台意味着你网站上的每个文件都是你自己放在那里的。事实上，在紧要关头，你的网站可以只包含两个文件；`index.html`和`mycode.js`。第一个可能是这样的:

```
<!DOCTYPE html>
<html>
<head>
<script type="text/javascript" src="mycode.js"></script>
</head>
<body>
</body>
</html> 
```

我知道它只有一行，但是因为 JavaScript 100%控制你的网页上发生的一切，如果你想这样做，它可以提供标题、CSS 和所有内容。这对我来说有点太原始了，但如果它能让你满意，那就随它去吧。你甚至可以用 VIM 或 EMACS 在命令行编码。您需要手动将文件 SSH 到您的服务器，除非您使用像 Kate(在 Linux/KDE 上)这样的代码编辑器，它将远程文件视为本地文件。

另一个文件，`mycode.js`有你所有的程序代码；让网络应用成为现实的一切。布局、内容、造型、响应度等等；平台让所有的事情变得更容易做。

### 选项 2 - WordPress(或其他内容管理器)

WordPress 长期以来一直受到博客作者和小型网站建设者的欢迎，但从这些普通的基础开始，它稳步增长，现在已经占据了网络上 1000 万个顶级网站的 1/3 以上。[WP 初学者](http://wpbeginner.com/)有一个 40 人的列表，从

1.  TechCrunch。
2.  《纽约客》。
3.  BBC 美国台。
4.  彭博专业。
5.  星球大战官方博客。
6.  多样化。
7.  索尼音乐。
8.  MTV 新闻。

所以，如果你认为 WordPress 只是为博客服务的话，请三思。

另一个误解是 WordPress 碍事，用复杂的主题占据了版面。嗯，这实际上取决于你。建立一个只包含你放进去的内容的“干净页面”真的非常简单。我很快会告诉你如何做，但同时我会强调使用 WordPress 的一些优势。

*   对于日常任务，不需要直接访问服务器。你可以在 WordPress UI 中做任何事情，让它负责将文件传输到服务器。事实上，他们中的许多人进入了数据库。
*   文本编辑。对于所有文件，包括原始程序代码，你可以使用 WordPress 编辑器。这不是最好的，但已经足够好了，尤其是对于你可能想要的附加页面，比如帮助或关于我。当然，这并不妨碍您使用其他 IDE 工具。
*   媒体管理。WordPress 为你管理所有的图片，为你提供一个可以在你的代码中使用的每张图片的 URL。
*   反应灵敏。当你在智能手机上运行你的网站时，WordPress 会帮助你对字体大小、边框等进行适当的调整，以保持一个好看的显示。
*   用户管理。允许您为具有不同访问级别的人设置登录，并使某些页面保持私密或受密码保护。
*   插件。这些是扩展 WordPress 的机制。有数以千计的可用插件，你可以自己编写它们来添加任何你喜欢的功能。
*   它是免费的。

对于本文的其余部分，我将假设选择是 WordPress。核心编码人员可能仍然会发现其中一些有趣的部分，但其他部分他们将不得不跳过。

## 设置 WordPress

如果你选择了一个好的主机服务，他们会选择在你的网站上快速安装 WordPress 并建立一个数据库。最困难的部分是找到这些选项在控制面板中的位置；之后其实就很简单了。

一旦你安装了 WordPress，它会向访问者显示一个默认的主页，并给你管理员权限去改变一些东西。如果这对你来说是全新的，我强烈建议你花些时间定制主页，哪怕只是为了告诉访问者你是谁。它将帮助你找到你在 WordPress 周围的路，并在网上建立你的存在。

为了支持网络应用的开发，以及添加内容，你可能需要对 WordPress 本身进行一些定制。这是完全正常的；系统就是为这种事情设计的。首先，每个页面的外观由一个主题控制；这是你网站上的一个 WordPress 目录中的文件集合(`wp-content/themes`)。主题决定了页面由哪些模块组成；页眉、页脚、边栏、菜单等等。您放入编辑器的内容只是进入其中一个块。当你开发一个 web 应用程序时，这可能不适合你；相反，你可能更喜欢不要这些东西，而用一张白纸来代替。

### 添加空白页模板

虽然这与 web 应用程序并没有太大的关系，但我会介绍它，因为了解它很方便。

主题为不同种类的页面提供了模板选择，因此主页通常会有不同于其他页面的模板。添加一个给你一个空白页的特殊模板是相当容易的；你所需要的就是这个 PHP 文件(它来自[这里是](https://www.wonderplugin.com/wordpress-tutorials/how-to-create-a-wordpress-page-without-header-menu-sidebar-and-footer/) ):

```
<?php
/**
 * Template Name: Clean Page
 * This template will only display the content you entered in the page editor
 */
?>

<html <?php language_attributes(); ?> class="no-js">
<head>
    <meta charset="<?php bloginfo( 'charset' ); ?>">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <?php wp_head(); ?>
</head>
<body>
<?php
    while ( have_posts() ) : the_post();   
        the_content();
    endwhile;
?>
<?php wp_footer(); ?>
</body>
</html> 
```

将此保存为名为`page-cleanpage.php`的新文件，并复制到主题文件夹中。编辑器的模板下拉列表中现在有了一个新的 Clean Page 选项，当选择该选项时，会给你一个空白页，你可以在其中放置你自己的代码和标记。就像从头开始滚动你自己的网页一样，除了现在你已经拥有了 WordPress 所有有用的特性，可以在需要的时候使用。

主题有时会由作者更新，当您安装更新时，您将丢失对主题所做的任何修改。为了防止这种情况发生，您可以创建一个子主题来包含您的自定义修改。或者，不要更新主题。

### WordPress 插件

接下来的事情就是安装一些有用的插件。WordPress 有一个包含数千个插件的库，这些插件是由公司和个人提供的，用于执行大量有用的服务。我会推荐几个你可能会觉得有用的。

**经典编辑器**
*“启用 WordPress 经典编辑器和老式编辑帖子屏幕，带有 TinyMCE、Meta 框等。支持扩展此屏幕的旧插件。*

人们对新的 WordPress 块编辑器 Gutenberg 热情高涨。许多人喜欢它，但是 web 应用程序的构建者大部分时间使用文本模式，对他们来说使用起来很不方便。所以这个插件给你一个传统和新的选择。还有其他几个类似的同样不错的插件，比如**禁用古腾堡**。强烈推荐。

**页眉和页脚脚本**
*“允许你在你的 WordPress 站点的页眉或页脚中插入代码或文本”。*

这个插件让你能够添加代码和样式到整个网站或者只是一个页面。因此，如果您需要 JavaScript 库，可以在这里添加。类似地，你可以添加你自己的 CSS 类和规则，这可能会覆盖那些已经被 WordPress 主题设置好的。您将在编辑页面的底部获得一个额外的字段，您可以在其中添加所有自定义的额外内容。强烈推荐。

**复印机**
*“迁移并备份你的 WordPress 文件和数据库的副本。快速地将网站从一个位置复制并移动到另一个位置。*

应定期进行备份；没有什么比因为一个简单的错误而失去所有工作更令人沮丧的了。Duplicator 创建备份，也可以用来在其他地方建立您的网站的副本。强烈推荐。

**EasyCoder**
*“通过嵌入简单的类似英语的脚本来控制你的帖子和页面的外观和行为，无需学习 JavaScript。”*

EasyCoder 为 JavaScript 编码提供了一个更简单的替代方案。它是一种类似英语的脚本语言，在浏览器中运行，可以做任何事情，从简单的交互到完整的网络应用。EasyCoder 网站有很多例子。

## 构建您的网络应用

您现在可以开始构建您的 web 应用程序了。有几种方法可以解决这个问题:

*   普通 JavaScript
*   JavaScript 加一个框架
*   EasyCoder 或其他高级脚本
*   其他的

### 香草 JavaScript

这个选项与我在上面列出的 WordPress 的替代方案 **No Platform** 大致相同，但是不同之处在于你有一个框架来运行你的代码。你可能会在你最喜欢的 IDE 中编写它——我认识的大多数人更喜欢 Visual Studio 代码——然后 SSH 到你的服务器，把它放在 WordPress 不知道的安装顶层的一个目录中，在那里它应该是安全的，不会被意外删除。然后使用页眉和页脚脚本编辑框添加一个链接，并使用浏览器的开发工具进行调试。

### JavaScript 加框架

在这里，您构建了一个完整的可部署的。js 文件使用 JavaScript 与 React，Angular，Vue 等。根据喜好，并像以前一样上传到您的服务器。您可能还需要添加一个到框架文件的链接。我不能说太多，因为这取决于你选择的工作框架。

### EasyCoder

这是相当不同的，因为你大部分是在 WordPress 编辑器中完成的。我将使用 EasyCoder 来实现我们的 web 应用程序的用户界面和业务规则，所以如果你希望 React 或 Angular，你可能会失望(但是嘿，不同也是好的)。它的工作方式是你写一个或多个类似英语的脚本(例子和文档在 [EasyCoder](https://easycoder.software) 网站)，然后把第一个脚本放在你的页面中一个特殊的`<pre>` HTML 元素中。当页面加载时，EasyCoder 插件编译并运行脚本。

### 其他

肯定有其他的选择。我只是不知道那是什么。

# 库组件

任何规模的大多数网络应用都不仅仅是一个用户界面。他们通常有一些核心功能，或者由同一个团队编写，或者作为第三方库模块引入，web 应用程序本身主要是利用这些功能。如果库模块是在团队之外开发的，那么它必须单独处理，但是通过识别项目中可以作为独立功能块剥离的部分，理所当然地应用这个原则是一个很好的策略。这就是 JavaScript 和 React 技能发挥作用并产生最大效果的地方。

关于组件使用的一个简单例子，看看地图上的演示 web 应用[，它包含一个实时的谷歌地图，当你与它交互时，它在同一页面*上做一些事情，但在地图本身之外*。谷歌地图以 JavaScript 库文件的形式出现。这是一个黑盒子；我们无法直接向它添加我们自己的功能，所以我们只能通过它的应用编程接口(API)来访问它。](https://hereonthemap.com)

这都是关于“关注点分离”设计模式的。地图组件不需要知道它在哪里运行，用户界面也不关心地图是如何工作的。事实上，它们是两个完全独立的项目，作为一个团队运作。

我为本系列选择的 web 应用程序也将采用这种设计模式。对于拼字游戏爱好者来说，这是一个完美的工具，一个变位词查找器，它接受一行文本——通常是某人的名字——并在字典中搜索它能找到的所有单词组合，这些组合将用完所有的字母，然后将它们显示为一个列表。这是一项计算密集型任务，将在几秒钟内返回一些结果，但可能需要几个小时才能穷尽所有可能性。核心功能一旦编写，就不太可能改变，所以它是构建库组件的理想候选。(我知道 Node.js 有一个`anagram`模块，但我很好奇自己写一个有多难。)

## 即将到来...

在本系列的下一部分中，我将展示 web 应用程序的用户故事以及实现它们所需的代码。

迈克尔·波德格在 [Unsplash](https://unsplash.com/search/photos/web?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的标题照片