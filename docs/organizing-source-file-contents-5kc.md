# 组织源文件内容

> 原文：<https://dev.to/conw_y/organizing-source-file-contents-5kc>

我们开发人员经常关注如何在文件系统中将文件组织到文件夹中。但是也许同样重要的是我们如何组织源文件的**内部内容。**

根据开源代码、团队成员的代码，当然还有我自己的经验，我已经为如何排序每个源文件的内容设计了一个蓝图。

我认为坚持一致的订购模式是有好处的。如果文件结构良好，初学者更容易理解源代码。对于更有经验的人来说，快速解析文件并找到他们想要的东西也更容易。

# 原则

我使用三个原则作为我如何排序文件内容的一般指南:

1.  先抽象后具体。
2.  先扩大范围，后缩小范围。
3.  先定义后使用。

基于这些原则，我为如何整理我的文件制定了一个粗略的大纲。

注意:这些例子是用 Typescript 和 React 编写的，但是您可以很容易地将这些概念应用到其他主要语言和框架中。

所以废话不多说...

# 蓝图

这是蓝图的摘要:

1.  注释标题
2.  编译器指令
3.  进口(第三方)
4.  导入(应用范围)
5.  导入(文件夹范围)
6.  接口和类型声明
7.  实用函数和常量(文件范围)
8.  文件的中心代码
9.  电子竞技

下面是完整的示例代码(单击可放大):

[![Screenshot of the entire example, with arrows pointing to where each of the sections are placed](img/d0ec208456b10eec4e4c44ae2b98672c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S4kU2VaR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p5bfbc30qm5he1dt6gar.jpeg)

现在，让我们详细了解每一部分。

## 1。注释标题

根据我为谁工作或者我正在合作哪个开源项目，我可能需要包含一个带有一些重要内容的评论头，比如许可证、版权声明等。我把这些放在最上面，放在其他东西之前。

这是因为注释头对文件来说是全局的(“较宽的范围在较窄的范围之前”)，所以最好把它放在顶部，在那里它可能是第一个被读取的。

示例:

```
/*****************************************************************************
 * ListApp, Copyright (c) 2019, Jonathan Conway
 * All rights reserved.
 *
 * ListApp is licensed under the Apache License, Version 2.0 (the
 * "License"); you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 * http://www.apache.org/licenses/LICENSE-2.0.
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
 * WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
 * License for the specific language governing permissions and limitations
 * under the License.
 *
 * ListApp includes source code licensed under additional open source
 * licenses. See the Open Source Licenses file (LICENSES.md) included with
 * this source code distribution or the Licensing information page available
 * at runtime from the About dialog for additional information.
 *****************************************************************************/ 
```

Enter fullscreen mode Exit fullscreen mode

## 2。编译器指令

我需要告诉编译器或 transpiler 的任何事情通常都是先处理，先于任何代码。在 C++中，这将是预处理宏。在 C#中，定义宏。在 Javascript 中，指令。

这是因为编译器指令定义了逻辑上优先于应用程序代码的行为(“先定义后使用”)，所以我更愿意在阅读任何使用它们的代码之前阅读它们。

示例:

```
"use strict"; 
```

Enter fullscreen mode Exit fullscreen mode

## 3。进口(第三方)

我将第三方或应用程序外部的所有导入库放在一起。

这是因为第三方导入在我的所有导入中具有最大的范围——它们可以被我的整个应用程序访问(“较大的范围先于较小的范围”)。

我通常不太关心它们的排序方式，比如按字母顺序，因为理想情况下，我希望这个列表非常短，这样就没有排序的必要。如果这个列表很长，我认为这是一个坏消息，表明这个模块有太多的职责。

然而，我通常会将最具全球相关性的进口放在顶部，而将更具体的进口放在较低的位置。

示例:

```
import * as React from "react";
import { Component } from "react"; 
```

Enter fullscreen mode Exit fullscreen mode

## 4。导入(应用范围)

接下来是我的整个应用程序范围内的模块的导入，比如全局类型、全局状态或通用工具。

这是因为应用程序范围导入的范围比第三方导入的范围窄，但比文件夹范围导入的范围宽(“范围宽于范围窄”)。

(边注:我试图使用[桶式进口](https://basarat.gitbooks.io/typescript/content/docs/tips/barrel.html)这些，只是因为它更干净，更封装。)

示例:

```
import { Item } from "../types"; 
```

Enter fullscreen mode Exit fullscreen mode

## 5。导入(文件夹范围)

最后(对于导入)是范围与当前文件相同的文件夹中的任何导入。

这是因为文件夹范围的导入在所有导入中具有最窄的范围(“较宽的范围先于较窄的范围”)。

示例:

```
import { ItemCard } from "./ItemCard"; 
```

Enter fullscreen mode Exit fullscreen mode

## 6。接口和类型声明

任何接口和类型声明都在这里声明。

这是因为接口和类型是抽象的(“先抽象后具体”)，并且它们必须在实现它们的代码之前(“先定义后使用”)。

示例:

```
interface ItemsListProps {
  readonly items: readonly Item[];
  readonly fetchItems: (range: { from: number, to: number }) => void;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 7。实用函数和常量(文件范围)

文件中中央代码所需的实用函数和常量放在这里。这些是按照定义和用法的顺序排列的。

这是因为实用函数可能比调用它们的代码更通用(“先抽象后具体”)，而且实用函数和常量的作用域都是整个文件(“先宽后窄”)，并且在声明后使用(“先定义后使用”)。

示例:

```
const ITEMS_PER_PAGE = 5;

const calculateRangeToFetch = (currentPageNumber: number) => ({
  from: (currentPageNumber * ITEMS_PER_PAGE),
  to: ((currentPageNumber + 1) * ITEMS_PER_PAGE)
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 8。中央代码

我创建的大多数源文件都有一段核心代码，其他的都是围绕这段代码构建的。

这通常是一个中心功能、组件、类或其他类型的结构。它通常是我从文件中导出的唯一结构，但是如果需要的话，我可能会导出其他结构(比如接口或类型)。

作为文件的中心部分，代码位于中心是有意义的，在它所依赖的所有代码(导入、声明、实用程序)之后，但在任何导出之前。

示例:

```
class ItemsList extends Component<ItemsListProps> {
  public state = {
    currentPageNumber: 1
  };

  private fetchMore() {
    this.props.fetchItems(
      calculateRangeToFetch(
        this.state.currentPageNumber));
    this.setState({
      currentPageNumber: this.state.currentPageNumber + 1
    });
  }

  public render() {
    return (
      <div>
        <ul>
          {this.props.items.map((item, index) =>
            <ItemCard key={index} item={item} />
          )}
        </ul>
        <button onClick={() => this.fetchMore()}>
          Fetch {ITEMS_PER_PAGE} more
        </button>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 9。出口

最后，我得到了导出语句，它向外界公开了文件中前面声明的结构。

这是因为，如果我想看到哪些结构被导出到文件之外，我可以快速滚动到末尾，找到它们。此外，导出总是预先假定可导出的结构，即已经声明的结构(“使用前定义”)。

示例:

```
export { ItemsList }; 
```

Enter fullscreen mode Exit fullscreen mode

# 自动化组织

一旦你决定了如何组织你的源文件，组织它们的实际任务是自动化的一个很好的候选。

在我自己的例子中，在 Visual Studio 代码中使用 Typescript，我在保存时使用了[运行代码操作，效果很好。启用此设置后，我只需将](https://code.visualstudio.com/updates/v1_23#_run-code-actions-on-save)[有序导入](https://palantir.github.io/tslint/rules/ordered-imports/)规则添加到我的`tslint`文件中，将`grouped-imports`设置为`true`，它使用了本文中给出的相同分组。

# 源文件就像建筑物

我喜欢把源文件比作一座建筑。

一个建筑(典型的！)有清晰的入口和出口，并以这样一种方式组织，参观者逐渐被引入到建筑的布局中。例如，入口可能通向带有多个标签门的走廊，或者通向一个平台，该平台可以俯瞰一个大型开放空间，从该空间可以看到建筑物其他部分的入口。

同样，源文件的内容也应该有一个清晰的顺序，这样浏览者在从上到下浏览时，就可以逐步了解文件的主要结构，并迅速形成文件内容的基本思维“地图”。

我希望你会发现这个想法有用，并调整它，以适应自己的项目！

# 进一步阅读

*   鲍伯·马丁的“干净代码”(“第 5 章:格式”中的“垂直格式”部分)。
*   [打字稿《深潜》](https://basarat.gitbooks.io/typescript/)作者[巴萨拉特·阿里·赛义德](https://legacy.gitbook.com/@basarat)