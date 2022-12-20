# 维护 Dojo 存储的状态

> 原文：<https://dev.to/odoenet/maintain-state-with-dojo-stores-3n1b>

我们之前看了如何使用应用程序的上下文来维护 Dojo 容器的状态。概括地说，容器基本上是高阶组件，您可以用它来为小部件添加状态管理。

使用带有容器的上下文使这个过程变得相当简单，但是如果您想要连接多个带有共享状态的容器和路由，您可能想要开始考虑使用 [Dojo 存储](https://github.com/dojo/framework/tree/master/src/stores)。

Dojo 通过使用[进程](https://github.com/dojo/framework/tree/master/src/stores#processes)来执行[命令](https://github.com/dojo/framework/tree/master/src/stores#commands)并根据您的状态改变[操作](https://github.com/dojo/framework/tree/master/src/stores#operations)来存储工作。您不需要定义您的操作，Dojo 为您提供了这些操作。这些操作基于 [JSON 补丁格式](http://jsonpatch.com/)。它们目前支持添加、删除、替换和测试。如果你不熟悉这些操作或者它们看起来很吓人，不要担心，它们比乍看起来要简单得多。

为了尝试并掌握一切是如何工作的，让我们创建一个探索[星球大战 API](https://swapi.co/) 的小应用程序。

## API 浏览器小工具

我们要做的第一件事是创建一个可以探索 API 的小部件。我将使用一个[列表框](https://github.com/dojo/widgets/tree/master/src/listbox)来显示 API 的端点，并使用一个[文本区域](https://github.com/dojo/widgets/tree/master/src/text-area)来显示所选端点的原始 JSON 结果。

```
// src/widgets/APIExplorer.tsx
import { tsx } from "@dojo/framework/widget-core/tsx";
import { WidgetBase } from "@dojo/framework/widget-core/WidgetBase";
import { watch } from "@dojo/framework/widget-core/decorators/watch";
import Listbox from "@dojo/widgets/listbox";
import TextArea from "@dojo/widgets/text-area";
import theme from "@dojo/themes/dojo";
import * as css from "./styles/APIExplorer.m.css";

import { ExplorerProperties, Item } from "../interfaces";

export class APIExplorer extends WidgetBase<ExplorerProperties> {
  @watch() index = 0;
  @watch() label = "";

  onAttach() {
    this.properties.fetchAllResults();
  }
  protected render() {
    return (
      <div classes={css.root}>
        <Listbox
          theme={theme}
          key="listbox1"
          activeIndex={this.index}
          widgetId="listbox1"
          optionData={this.properties.items}
          getOptionLabel={(option: Item) => option.label}
          getOptionSelected={(option: Item) => option.label === this.label}
          onActiveIndexChange={(index: number) => {
            this.index = index;
          }}
          onOptionSelect={(option: Item, index: number) => {
            this.label = option.label;
            this.properties.fetchData(option);
          }}
        />
        <TextArea rows={15} theme={theme} value={this.properties.result} />
      </div>
    );
  }
}

// src/interfaces.ts
export interface ExplorerProperties {
  items: Item[];
  result: string;
  fetchAllResults: () => void;
  fetchData: (item: Item) => void;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个小部件有一些本地状态来管理 Listbox 的选定值，但是除此之外，它依赖于传递给它的属性来显示任何有用的内容。我将使用一个容器将这些属性传递给这个小部件。但是，首先，我应该如何进行查询并更新我的应用程序状态呢？

## 流程

进程用于对应用程序存储执行命令。正是在这些过程中，您将为您的应用程序做大部分繁重的工作。这是您可以执行查询、转换、过滤、验证等操作的地方。Dojo 中有一些工厂函数可以帮助您创建命令。

```
// src/processes/starwarsProcesses.ts
import {
  createProcess,
  createCommandFactory
} from "@dojo/framework/stores/process";
import { replace } from "@dojo/framework/stores/state/operations";

import { State, Item } from "../interfaces";

// commandFactory typed to my application state
const commandFactory = createCommandFactory<State>(); 
```

Enter fullscreen mode Exit fullscreen mode

在这个应用程序中，我将使用**替换**操作来更新我的状态。如果我在我的命令中向一个数组添加项目，我可以使用助手的**和 **add** 操作在特定索引处向数组*中插入新项目，从而做一些有趣的事情。***

让我创建一个命令来获取星球大战 API 的所有可用端点。

```
// src/processes/starwarsProcesses.ts
const fetchItems = commandFactory<Item>(async ({ path }) => {
  const response = await fetch("https://swapi.co/api/");
  const json = await response.json();
  const items: Item[] = Object.keys(json).map(key => {
    return {
      label: key,
      value: json[key]
    };
  });
  return [replace(path("items"), items)];
}); 
```

Enter fullscreen mode Exit fullscreen mode

我使用我的命令工厂来创建一个函数，该函数将生成我的查询，然后返回一个操作数组。为什么是数组？因为我可能希望用一个命令完成多个状态更新。在这种情况下我使用**【replace(path(" items ")，items)】**。这乍一看可能很奇怪，但是 path 方法是在我的状态中指定属性路径的一种方式。如果我想使用来自我的状态的值，我可以使用一个 *get* 助手并编写 **get(path("items"))** ，这将返回项目数组。

起初，我不确定我对这种访问状态的方法感觉如何，但是它非常适合作为一种与状态交互的功能性和反应性的方法，我已经真正喜欢上了这种方法。这种行为非常类似于使用[镜头](https://medium.com/javascript-scene/lenses-b85976cb0534)。

现在，我将创建一个命令来获取其中一个选定端点的结果。

```
// src/processes/starwarsProcesses.ts
const fetchResult = commandFactory<Item>(async ({ path, payload }) => {
  const response = await fetch(payload.value);
  const result = await response.json();
  return [replace(path("result"), JSON.stringify(result, undefined, 2))];
}); 
```

Enter fullscreen mode Exit fullscreen mode

与前面的命令非常相似，我将获取一个端点的结果，但是该端点 URL 来自传递给我的命令的一个**有效负载**对象。我已经输入了这个有效载荷，使其具有一个**值**属性，但是它可以是传递给执行这个命令的进程的任何参数。我们马上就会看到这是如何实现的，但是首先我们需要创建两个用于执行这些命令的进程。

```
// src/processes/starwarsProcesses.ts
export const fetchItemsProcess = createProcess("fetch-items", [fetchItems]);
export const fetchResultProcess = createProcess("fetch-result", [fetchResult]); 
```

Enter fullscreen mode Exit fullscreen mode

我们创建两个进程来对我的状态执行两个不同的操作。我觉得有趣的是，我可以让一个进程执行多个命令，这些命令反过来又可以对我的状态执行多个操作。我还没有这样做过，但是我发现我可以很棒！

## 容器

让我们将小部件包装在一个容器中，该容器将使用我们的流程来连接属性和方法。

```
// src/containers/APIExplorerContainer.ts
import { Store } from "@dojo/framework/stores/Store";
import { StoreContainer } from "@dojo/framework/stores/StoreInjector";
import { APIExplorer } from "../widgets/APIExplorer";

import { State } from "../interfaces";

import {
  fetchItemsProcess,
  fetchResultProcess
} from "../processes/starwarsProcesses";

function getProperties(store: Store<State>): any {
  const { get, path } = store;

  return {
    items: get(path("items")),
    result: get(path("result")),
    fetchAllResults: fetchItemsProcess(store),
    fetchData: fetchResultProcess(store)
  };
}
// Use a StoreContainer
export const APIExplorerContainer = StoreContainer(
  APIExplorer,
  "state",
  { getProperties }
); 
```

Enter fullscreen mode Exit fullscreen mode

我们使用一个名为 **StoreContainer** 的特定容器，它将我们的存储注入到我们的 **getProperties** 方法中。这个方法就是如何将属性从容器传递给包装的小部件。

您可以在这里看到 store 有助手方法， **get** 和 **path** ，我之前提到过它们来访问 store 上的属性值。我现在可以将对商店执行命令的进程作为方法传递，我的小部件希望这些方法成为其属性的一部分。

一旦完成，我需要用我的应用程序注册我的商店并注入它。

```
// src/main.tsx
const store = new Store();
const registry = registerStoreInjector(store);

class App extends WidgetBase {
  protected render() {
    return <APIExplorerContainer />;
  }
}

const r = renderer(() => <App />);
r.mount({ registry }); 
```

Enter fullscreen mode Exit fullscreen mode

我使用一个名为 **registerStoreInjector** 的助手，然后将这个存储注入到我在容器中使用的一个命名状态中。

你最终在 codesandbox 上得到的是这样一个应用程序[。](https://codesandbox.io/s/7m9q47yvj?fontsize=14&module=%2Fsrc%2Fprocesses%2FstarwarsProcesses.ts)

## 总结

这里发生了很多事情，但归结起来就是以下几个步骤。

*   创建对存储执行命令的进程
*   将小部件包装在容器中，以传递进程和存储属性
*   将存储注入应用程序

商店的任何后续更新都将传递给小部件。你还可以利用你的商店做更多的事情。我可以将我的小部件包装在一个 [StoreProvider](https://github.com/dojo/framework/tree/master/src/stores#advanced) 中，并将商店属性传递下去，并手动[订阅商店更改](https://github.com/dojo/framework/tree/master/src/stores#subscribing-to-store-changes)。订阅存储更改在您的应用程序中可能是一个非常有用的工具，例如用于管理小部件可见性的一些粒度控制。

流程甚至提供了添加[中间件](https://github.com/dojo/framework/tree/master/src/stores#middleware)的能力，因此您可以添加日志记录和认证，或者有效负载验证，而不必用额外的代码和可能的额外错误源污染您的流程。

最后，我愉快地学习了如何在我的应用程序中使用进程和存储。它们在管理状态方面提供了很大的灵活性，我已经可以看到随着应用程序的增长，它们在构建应用程序方面会有多么有用！

请务必[订阅时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！