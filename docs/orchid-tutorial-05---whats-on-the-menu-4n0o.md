# 兰花教程 05 -菜单上有什么？

> 原文：<https://dev.to/cjbrooks12/orchid-tutorial-05---whats-on-the-menu-4n0o>

## 简介

现在我们已经了解了兰花如何使用集合来查找页面，让我们应用这些知识和一些新概念来构建动态菜单。

在继续之前，请确保您已经按照前面的教程进行了操作，并且已经使用`gradle orchidServe`启动了您的本地兰花服务器。在本教程中，我们将基于这个例子。

你可以自己跟随这个教程，或者在 [OrchidTutorials 库](https://github.com/JavaEden/OrchidTutorials/tree/master/05)中找到它的源代码。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [兰花](https://github.com/orchidhq) / [兰花](https://github.com/orchidhq/Orchid)

### 构建和部署与您一起成长的漂亮的文档站点

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Orchid](img/eb7f166eb445fa57d6c2364a5a005cc8.png "Orchid")](https://orchid.run/) 
**构建和部署与您一起成长的精美文档网站**

* * *

[![Current Version](img/0c72744f2b1b9c09e81058ea16d017a3.png "Current Version")](https://bintray.com/javaeden/Orchid/OrchidCore/_latestVersion)[![License: LGPL-3.0](img/0561c8fa72f81c47a6b5ec394037be00.png "License: LGPL-3.0")](https://www.gnu.org/licenses/lgpl-3.0)[![Linux and Mac Build Status](img/1a217af32dacd351a6eb6e5929c3a579.png "Linux and Mac Build Status")](https://travis-ci.org/orchidhq/orchid)[![Windows Build status](img/4c3d58f4c9f5f0e4e69e504c56766560.png "Windows Build status")](https://ci.appveyor.com/project/cjbrooks12/orchid/branch/dev)[![Code Coverage](img/7d3a3ce870c280f4289105e25d15df83.png "Code Coverage")](https://www.codacy.com/app/cjbrooks12/Orchid?utm_source=github.com&utm_medium=referral&utm_content=orchidhq/orchid&utm_campaign=Badge_Coverage)

[快速入门](https://orchid.run/wiki/user-manual/getting-started/quickstart)[文档](https://orchid.run/wiki/user-manual/getting-started)[教程](https://orchid.run/wiki/learn)[展示](https://orchid.run/showcase)[支持](https://gitter.im/JavaEden/Orchid)

## 快速启动

*   [![Gradle](img/d7b7758247fd5d9a702ccc8fef4a8a78.png "Gradle")](https://orchid.run/wiki/user-manual/getting-started/quickstart#gradle)
*   [![Maven](img/333008a337166354ec2e3aae317f2446.png "Maven")](https://orchid.run/wiki/user-manual/getting-started/quickstart#maven)
*   [![SBT](img/f083f972225ab4cd2ca281f78814ec5b.png "SBT")](https://orchid.run/wiki/user-manual/getting-started/quickstart#sbt)
*   [![Deploy to Netlify](img/9df7dbf561b0947dd4e9a437be4988c8.png "Deploy to Netlify")](https://app.netlify.com/start/deploy?repository=https://github.com/orchidhq/OrchidStarter)

## 证明文件

兰花的用户手册将带您了解兰花的主要功能，并让您更深入地了解每个主题和功能。

[文档](https://orchid.run/wiki/user-manual/getting-started)

## 教程

有几个教程旨在引导您从头开始构建一个兰花站点。所有教程的源代码也可以在[或教程库](https://github.com/orchidhq/OrchidTutorials)中找到。

[教程](https://orchid.run/wiki/learn)

## 显示优点的东西

查看陈列柜，看看兰花的行动。

[展示区](https://orchid.run/showcase)

## 支持

### 贡献者

这个项目的存在要感谢所有做出贡献的人。要参与，请见[投稿. md](https://github.com/orchidhq/orchid/blob/dev/.github/CONTRIBUTING.md)

[![Contributors](img/73f9b90f5c85df8ee8758e01a52152ba.png "Contributors")](https://github.com/orchidhq/orchid/graphs/contributors) 

### 巴克斯

感谢我们所有的支持者！<g-emoji class="g-emoji" alias="pray" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f64f.png">🙏</g-emoji>

[![Backers](img/4b5ae13c41799627fe67f964c4f987f9.png "Backers")](https://opencollective.com/orchidssg#backers) 

### 赞助商

通过成为赞助商来支持这个项目。您的徽标将显示在此处，并带有指向您网站的链接。

[![Sponsor 1](img/5855ef0cb57006e34da0600a502f1e4a.png "Sponsor 1") ](https://opencollective.com/orchidssg/sponsor/0/website) [ ![Sponsor 2](img/9637b83e9d1de20c4a680c1c7ae05b5d.png "Sponsor 2") ](https://opencollective.com/orchidssg/sponsor/1/website) [ ![Sponsor 3](img/d044bb6c0851431fc8586688f1f5cf55.png "Sponsor 3") ](https://opencollective.com/orchidssg/sponsor/2/website) [ ![Sponsor 4](img/ffe27bf4033460ee03426c33a400e613.png "Sponsor 4") ](https://opencollective.com/orchidssg/sponsor/3/website) [ ![Sponsor 5](img/1feb04f8ae16bff9e8a659140cc468a6.png "Sponsor 5") ](https://opencollective.com/orchidssg/sponsor/4/website) [ ![Sponsor 6](img/ee6b82364437fe7a96935dbeae058db3.png "Sponsor 6") ](https://opencollective.com/orchidssg/sponsor/5/website) [ ![Sponsor 7](img/05a9d69c04ac76393e885fa6a1eb327a.png "Sponsor 7") ](https://opencollective.com/orchidssg/sponsor/6/website) [ ![Sponsor 8](img/eac5c511c942df18d370017a0b432d09.png "Sponsor 8") ](https://opencollective.com/orchidssg/sponsor/7/website) [ ![Sponsor 9](img/78e4d5e5cec08c9057a72dba80203d6f.png "Sponsor 9") ](https://opencollective.com/orchidssg/sponsor/8/website) [![Sponsor 10](img/e723a91c6047bd10234de9ce09470c39.png "Sponsor 10")](https://opencollective.com/orchidssg/sponsor/9/website) 

### 许可证

Orchid 是在 GNU 宽松通用公共许可证(LGPL-3.0)下许可的开源软件。请看 [License.md](https://github.com/orchidhq/orchid/blob/dev/License.md)

### 接触

…

</article>

[View on GitHub](https://github.com/orchidhq/Orchid)

## 缩放你的站点

我们的站点目前有四个页面:休斯顿、达拉斯和奥斯汀的每个业务位置各有一个页面，这些页面被认为是在“位置”页面组中，因为它们都位于站点 URL 结构中的`/locations/...`；还有一页列出了所有地点。随着我们业务的增长，我们将希望在我们的网站上添加更多的内容，以便客户可以在网上找到他们需要的一切。因此，让我们为我们的服务和员工在几个页面“组”中再添加几个页面。将以下页面添加到您的站点中(您可以暂时将这些文件留空)。

```
└── pages/
    ├── locations/
    |   ├── houston.md
    |   ├── dallas.md
    |   └── austin.md
    ├── services/
    |   ├── home-repair.md
    |   ├── electrical.md
    |   └── security.md
    └── staff/
        ├── billy-bob.md
        └── john-doe.md 
```

Enter fullscreen mode Exit fullscreen mode

作为额外的练习，尝试为这些组创建索引页面。

## 主题定制

在我们开始向站点添加菜单之前，我们首先必须熟悉如何配置站点。

兰花在你的资源的根目录下有一个特殊的文件，`config.yml`。这个文件是一个 YAML 配置文件，您可以在其中放置您需要的所有配置。以下是一些可以从`config.yml`定制的东西的例子:

*   主题颜色
*   主题菜单
*   要包括或排除哪些插件
*   插件参数
*   一些核心的兰花功能

首先，让我们为主题选择一个更好的颜色。因为我们在这个例子中使用了 BsDoc 主题，所以将以下内容添加到您的`config.yml`文件中:

```
theme:
  primaryColor: '#dd9999' 
```

Enter fullscreen mode Exit fullscreen mode

一旦你的网站重建，你会发现以前是紫色的东西现在变成了沙红色！BsDoc 主题被设置为重新编译它的 SCSS 样式表，为它的主要颜色注入配置值。在后面的教程中，我将更深入地介绍这个过程，以及如何在任何主题中找到所有可用的配置，但是现在，知道`config.yml`是定制站点和主题的地方就足够了。

现在我们准备开始构建我们网站的菜单了！

## 主题菜单

Orchid 中的大多数主题至少有一个菜单区域，这是站点的主要导航，有些主题还包括其他区域的菜单，比如页脚。不管菜单在你的主题中的什么位置，它总是以同样的方式配置:在`config.yml`中的主题配置中。让我们向之前使用的主题配置添加一个菜单:

### 链接菜单项

```
theme:
  primaryColor: '#dd9999'
  menu:
    - type: 'link'
      title: 'Locations'
      url: 'http://localhost:8080/locations'
    - type: 'link'
      title: 'Services'
      url: 'http://localhost:8080/services'
    - type: 'link'
      title: 'Staff'
      url: 'http://localhost:8080/staff' 
```

Enter fullscreen mode Exit fullscreen mode

这将在主题的主导航栏中生成一个包含 3 个项目的菜单，每个项目都指向我们每个页面组的索引页面。让我们把这个配置再分解一下，因为这个配置的结构在 Orchid 的其他地方也有使用。

在`config.yml`中我们的`theme`对象的`menu`属性应该是一个菜单项列表。每个菜单项都应该包括一个`type`字段，它告诉兰花要使用的菜单项的类型。有许多不同的菜单项类型；一些例子是`link`(在例子中使用)创建一个指向任意 URL 的菜单项，以及`page`在你的站点中查找一个页面并链接到它。

菜单项在每个页面上被动态评估，并且可以添加任意数量的项。`url`和`page`将分别在菜单中添加 0 或 1 个项目(取决于它们是否配置正确以及是否能找到页面)，但是其他类型如`pages`将为站点中设置的每个静态页面添加一个指向该页面的菜单项。不仅如此，菜单项本质上是递归的，其中菜单项可能包含子菜单，子菜单可能包含子菜单，等等。注意，不是每个主题都支持完全递归的菜单，通常只支持一两层嵌套。

每种类型的菜单项还可以声明任意数量的选项进行额外的定制。上面的`link`项需要 2 个附加属性:一个要链接的`url`和一个`title`。但是这些`link`项并不理想，原因和前面教程中描述的一样:链接被硬编码到开发 URL。让我们更改这些菜单项，让 Orchid 动态地生成实际的链接，根据需要无缝地为开发或生产而更改。

### 页面菜单项

```
theme:
  primaryColor: '#dd9999'
  menu:
    - type: 'page'
      itemId: 'Locations'
    - type: 'page'
      itemId: 'Services'
    - type: 'page'
      itemId: 'Staff' 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们没有使用`link`菜单项，而是使用`page`。希望您能认出这些菜单项上的`itemId`属性；如果没有，你可能想回到前面的教程来学习链接函数。这是因为`page`菜单项类型(以及许多其他类型)定位和链接页面的方式与该教程中描述的完全相同。但是作为一个快速复习:你通常可以通过将`itemId`设置为你想要链接到的页面的标题来链接你站点中的适当页面，并且`collectionType`和`collectionId`可以通过过滤特定集合中的页面来帮助确保你链接到你所期望的页面。

## 页面菜单

主题不是唯一可以定义菜单的东西。对于单个页面来说，包含描述页面内容的菜单，或者包含链接到相关页面的菜单是很常见的。页面菜单的配置和主题菜单完全一样，只是在页面的前端而不是`config.yml`。除此之外，两者之间的菜单生成方式完全没有区别。因此，让我们看看其他几个菜单项类型，它们将真正把你的网站菜单提升到一个新的水平！

### 静态页面菜单项

我们用来设置位置、服务和人员页面的 Pages 插件也包括一个`pages`菜单项类型。该菜单项类型将显示插件正在生成的所有页面，可以选择将其过滤到特定的页面组。让我们使用这种菜单项类型，以便每个页面创建一个链接到其组中所有其他页面的菜单。

在所有的位置页面中，将以下代码片段放在首页:

```
--------
menu:
  - type: 'pages'
    group: 'locations'
-------- 
```

Enter fullscreen mode Exit fullscreen mode

现在，当导航到我们的任何位置页面时，您将看到一个侧栏，其中包含我们所有位置的链接。您还会注意到，如果菜单项与您当前所在的页面匹配，BsDoc 主题会突出显示该菜单项，因此您总是知道自己在站点中的位置！通过在菜单项配置中指定`group`，只有“位置”页面被添加到菜单中，但是省略它将包括*所有的*静态页面。

您可以对其他页面组进行同样的操作，但是我将把它作为一个练习留给您。

### 页面 id 菜单项

现在我们有了彼此链接的位置页面，现在让我们让每个页面都为自己生成链接。菜单项也可以链接到页面内的 id，而不是链接到外部 URL 或内部页面。`pageIds`菜单项也是静态页面插件的一部分，它会在页面内容中找到所有带有 ID 的标题，并生成锚定到该 ID 的菜单项。兰花的 Markdown 处理器设置为生成每个标题的 id，所以让我们开始添加一些部分到我们的位置页面与标题链接。

例如，在`pages/locations/houston.md`中我们添加以下内容。如果您愿意，可以在其他位置页面添加类似的内容作为另一个练习。

```
# About Our Houston Location

Duis et mauris in leo aliquam bibendum et ut purus. Nulla sagittis volutpat massa non vestibulum.  

## Specialty Services

Pellentesque lorem magna, porttitor sed massa vitae, vestibulum volutpat magna. 

## All Staff

Vestibulum tristique finibus est, sed suscipit dui commodo quis. Duis condimentum in neque at auctor. 

# Reviews
 - Sed venenatis nibh a quam efficitur accumsan. 
- Vivamus in consectetur magna. 
- Donec viverra lorem nunc, eu finibus erat posuere ut. 
- Sed in leo ac est suscipit euismod a vitae sem. 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们将`pageIds`菜单项添加到这个页面。为了清楚起见，我还在页面自身链接和链接到其他位置页面的链接之间添加了一个`separator`菜单项。

```
--------
menu:
  - type: 'pageIds'
    structure: 'nested'
  - type: 'separator'
    title: 'All  Locations'
  - type: 'pages'
    group: 'locations'
-------- 
```

Enter fullscreen mode Exit fullscreen mode

完美！Orchid 现在正在通读该页面上的内容，取出它找到的所有 id，并动态生成将网页滚动到其内容中的那个位置的菜单项。您可以将这些页面 ID 菜单项定制为嵌套的(例如将`h2`标题创建为前一个`h1`的子菜单)，或者没有子菜单的平面。

这里需要注意的一点是，在生成这些页面 ID 链接时，只考虑固有的页面内容。主题或页面模板中的 id 不会被查看，但它只是所使用的 Markdown 文件中的实际内容。

## 结论

现在我们的网站看起来和功能都像一个完整的网站了！我们不仅有许多易于单独定制的页面，而且我们现在有一种方法来动态生成菜单，这些菜单将链接到我们所有的其他位置，无论我们添加多少新位置！

让我们回顾一下我们在本教程中学到的所有内容。

1.  `config.yml`是配置网站主题的中心位置，包括颜色和菜单。
2.  `config.yml`中的`theme.menu`属性设置主站点主题。在 BsDoc 主题中，这是主导航栏。
3.  菜单配置接受菜单项对象列表。每个对象的`type`属性告诉 Orchid 创建哪种类型的菜单项，并且每个菜单项类型还可以声明自己的选项，以便进一步定制。
4.  有些菜单项类型只会向生成的菜单中添加一项，有些则会添加多项。
5.  菜单项可以链接到外部 URL，查找并链接到站点内部的页面，或者链接到单个页面中的特定 id。查找内部页面链接使用与链接功能相同的`itemId`、`collectionType`、`collectionId`约定来定位页面。

* * *

本教程最初发布在[官方兰花文档](https://orchid.netlify.com/wiki/learn/tutorials/whats-on-the-menu)中。