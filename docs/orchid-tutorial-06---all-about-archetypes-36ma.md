# 兰花教程 06 -所有关于原型

> 原文：<https://dev.to/cjbrooks12/orchid-tutorial-06---all-about-archetypes-36ma>

## 简介

虽然兰花可以用于较小的网站，但当您开始使用原型来轻松地一次配置许多页面时，它才真正开始发光。本教程将向您介绍这个概念。

在继续之前，请确保您已经按照前面的教程进行了操作，并且已经使用`gradle orchidServe`启动了您的本地兰花服务器。在本教程中，我们将基于这个例子。

你可以自己跟随这个教程，或者在 [OrchidTutorials 库](https://github.com/JavaEden/OrchidTutorials/tree/master/06)中找到它的源代码。

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

## 大型网站的问题

实际上有数百个静态站点生成器，但几乎所有的都受到同一个问题的影响:如何轻松地管理一个可能包含数百甚至数千页面的站点？大多数网站会包含大量需要相似配置的页面，但是大多数生成器不提供任何方法来保持所有这些页面的配置同步。内置通用配置的支架页面只适用于新页面，但当您对网站进行大规模更改时，它一点帮助都没有。一定有更好的办法！

兰花的创建正是为了解决这类问题，即在长时间和多次迭代中大规模地构建和维护站点。它主要使用一种叫做**原型**的技术来解决这个问题，但是在我们深入研究这个解决方案之前，让我们确保我们完全理解这个问题。

### 重复配置

在我们为有多个地点的小企业构建网站的例子中，我们最终为每个地点创建了一个页面。最终网站有`/locations/houston`、`/locations/dallas`和`/locations/austin`的页面，在我们网站资源目录的`pages`目录下有相应的降价文件。这些文件的首页都包含了许多该地点特有的信息，如地址、电话号码或营业时间，但也包含了许多在*所有*地点都相同的信息。下面是我们的一个位置页面的全头版的例子:

```
--------
city: 'Houston'
state: 'TX'
postal_code: '12345'
address: '1234,  Example  Dr.'
phone: '(123)  456-7890'
business_hours:
  - 'M-F:  6am  -  9pm'
  - 'Sa:  6am  -  10pm'
  - 'Su:  Closed'
template: 'location' 
menu:
  - type: pageIds
    structure: nested
  - type: separator
    title: 'All  Locations'
  - type: pages
    group: 'locations'
-------- 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将这个前端问题标题分成两部分:一部分用于所有位置的公共配置值，另一部分用于该位置的特有配置值:

```
# unique to Houston location
city: 'Houston'
state: 'TX'
postal_code: '12345'
address: '1234,  Example  Dr.'
phone: '(123)  456-7890'
business_hours:
  - 'M-F:  6am  -  9pm'
  - 'Sa:  6am  -  10pm'
  - 'Su:  Closed'

# common to all locations
template: 'location' 
menu:
  - type: pageIds
    structure: nested
  - type: separator
    title: 'All  Locations'
  - type: pages
    group: 'locations'
-------- 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们可以继续将这种通用配置复制到所有新的位置，但它肯定不是理想的。它要求我们确保所有新位置都正确地复制了这一新配置，否则为我们建立一个脚手架来完成这一工作，这在未来的某个时间点肯定会过时。

### 配置不灵活

手动或通过搭建来管理许多页面的配置的另一个问题是，通过调整设置来找到最适合所有页面的方式非常困难。如果您使用前面显示的`location`页面模板创建几个页面，或者几百个页面，然后决定更改它，那么您将面临一条漫长而艰难的道路。使用静态配置和搭建对于长期的站点维护或迭代开发是行不通的。

## 兰花原型

兰花有一种独特的方式来解决这个问题，使用一种叫做*原型*的技术。主要思想非常简单:不要在许多页面的首页使用相同的配置，你可以把它放在`config.yml`中，兰花会把它当作首页来处理！

### “所有页面”原型

我们来看一个例子；将下面的代码片段添加到您的`config.yml`中，并从您的所有位置页面中删除通用配置:

```
allPages:
  template: 'location' 
  menu:
    - type: pageIds
      structure: nested
    - type: separator
      title: 'All  Locations'
    - type: pages
      group: 'locations' 
```

Enter fullscreen mode Exit fullscreen mode

看，你的网站看起来一模一样！只是现在，您没有在您的位置文件中复制一堆配置！通过将公共配置移动到`config.yml`中的`allPages`属性，您已经指示兰花使用配置数据*以及*到出现在每个页面首页的配置中。

### “页面组”原型

但是这个解决方案还不完美。`allPages`原型数据将用于兰花生成的每个页面的*上，这意味着我们所有的服务和员工页面也将开始使用我们设置的页面模板和菜单。我们只希望我们的位置页面有配置。幸运的是，兰花有许多不同的原型，这些原型更多地局限于您希望它们出现的页面。让我们看看另一个效果更好的:* 

```
pages:
  locations:
    template: 'location' 
    menu:
      - type: pageIds
        structure: nested
      - type: separator
        title: 'All  Locations'
      - type: pages
        group: 'locations' 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们没有将`allPages`处的原型数据放在`config.yml`处，而是放在`pages.locations`处。现在，当网站重建时，这些配置数据将只被拉入我们的位置页面，而不是服务或员工页面，正如我们所希望的那样！

### 这是怎么回事？

你会注意到`pages`是创建我们位置页面的 Pages 插件的名称，我们的位置都在`locations/`顶层子目录中，这给了它们一个`locations`的“组”。“静态页面”插件以这种方式坚持己见，并期望子目录中的页面是相关的，因此提供了自己的定制原型来帮助该组的配置。您也可以为其他组设置专用的“页面组”配置，它们会自动适应。

其他插件也都有自己固执己见的原型约定。例如,“Posts”插件允许从`config.yml`中为每个帖子的类别提取相似的配置，而“Wiki”插件为每个部分做同样的事情。

有许多不同类型的原型。大多数将从`config.yml`中提取数据，一些以完全不同的方式工作，但是它们都一起工作来获取您需要的配置值，而不必直接在页面的前端指定它们。

有这么多不同的方式来配置每一个页面，这可能有点棘手，但下一个教程将向您展示如何兰花的自我记录的性质和它的管理面板，让您找到所有这些信息。

### 使用多个原型

页面通常有多个可能的原型，您可以同时混合所有原型的配置值。例如，您可以使用“所有页面”原型将站点中的所有页面设置为使用给定的布局模板，然后使用“页面组”原型来自定义页面模板。但是，在使用多个原型配置页面时，需要记住一些事情:

*   对于单值(如字符串或数字):
    *   原型是优先的。如果多个原型提供相同的属性，则选择具有最高优先级的原型的值。例如，“页面组”原型比“所有页面”原型具有更高的优先级，因为它更加专门化，所以如果两个原型都设置了布局，那么将使用“页面组”原型设置的布局。
    *   页面首页指定的值*总是*覆盖原型值。例如，如果“页面组”原型设置了布局，那么可以通过在页面的首页设置布局来覆盖它。
*   对于列表或地图:
    *   列表项连接在一起。添加列表项的顺序是未指定的，但是大多数配置为列表的项(如菜单项)都有一种手动指定排序顺序的方式，这种方式不依赖于列表项最初出现的顺序。
    *   地图通过深度合并进行组合。如果多个原型包含同一个键，那么每个键上的值都用同样的策略递归地组合起来，其中更高优先级的原型覆盖单个值，列表和映射合并在一起。

利用这些知识，让我们使用“所有页面”和“页面组”原型来设置我们的位置页面。我们还将继续为服务和人员页面设置类似的原型配置。用以下代码片段替换`config.yml`内容:

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

allPages:
  template: 'page'
  menu:
    - type: pageIds
      structure: nested
      order: 1

pages:
  locations:
    template: 'location'
    menu:
      - type: separator
        title: 'All  Locations'
        order: 2
      - type: pages
        group: 'locations'
        order: 3 
```

Enter fullscreen mode Exit fullscreen mode

当站点重建时，您会注意到位置页面的`template`被设置为`location`，并且所有页面都有`pageIds`菜单项，这是从“所有页面”原型设置的，而“页面组”原型为所有位置添加了页面菜单项。

## 结论

随着您的站点范围和复杂性的增长，有必要对其进行相应的配置。有许多页面是相似的，其中许多可能有细微的差别，需要单独处理。兰花的原型给了你这样做的自由，所以让我们回顾一下:

1.  大型站点通常会导致大量的配置重复，这不能很好地扩展，并且一旦建立就很难更改。
2.  Orchid 允许从多个来源配置页面，而不仅仅是从它的前端。最常见的是，这个额外的配置来自同一个`config.yml`,您可以用它来配置站点的其余部分，所以一切都可以从一个位置很好地管理。
3.  在`config.yml`中，原型数据来自何处的细节由单个插件决定。对于如何构造对插件最有意义的原型数据，每个插件都有自己的约定。
4.  多个原型可以同时使用，并且是有序的，这样更具体的原型可以覆盖更一般的配置。当多个原型在同一个键上定义列表或映射时，它们会递归合并，而不是互相覆盖。

* * *

本教程最初发布在[官方兰花文档](https://orchid.netlify.com/wiki/learn/tutorials/all-about-archetypes)中。