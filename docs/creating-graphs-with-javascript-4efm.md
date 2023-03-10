# 用 JavaScript 创建图表

> 原文：<https://dev.to/emmabostian/creating-graphs-with-javascript-4efm>

图是一种数据结构，由带边的节点集合组成。图可以是有向的，也可以是无向的。

一个**有向**图包含功能类似于单行道的边。边从一个节点流向另一个节点。

例如，您可能有一个人和电影的图表，其中每个人都有几部最喜欢的电影，但电影没有最喜欢的人。

[![Directed graph](img/c47545d683173b9e518329108d36650c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kv5ciqHz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AbH2AHhFt9P_Tb8Opp6bhLQ.png)

无向图包含双向流动的边，类似于双向交通的双车道公路。

例如，您可能有一个宠物图，其中每只宠物都有一个主人，每个主人都有一只宠物。注意:双向箭头代表一条边，但是为了清楚起见，我画了两个箭头。

[![Undirected Graph](img/22405f731876334bdf2af7b152f63813.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m-auOpaK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A9x3Dhxs8Zrtfricvf1Cysw.png)

图表中的信息没有清晰的层次结构。

# 方法

我们要建立一个人和冰淇淋口味的图表。这将是一个有向图，因为人们可以喜欢某些口味，但口味不喜欢人。

我们将创建三个类:

*   `PersonNode`
*   `IceCreamFlavorNode`
*   `Graph`

### 人员节点

这个类将接受一个参数:一个人的名字。这将作为它的标识符。

`PersonNode`构造函数将包含两个属性:

*   `name`:唯一标识符
*   `favoriteFlavors`:IceCreamFlavorNodes 数组

另外，`PersonNode`类将包含一个方法:`addFlavor`。这将接受一个参数，一个`IceCreamFlavorNode`，并将其添加到`favoriteFlavors`数组中。

类定义如下:

```
class PersonNode {
  constructor(name) {
    this.name = name;
    this.favoriteFlavors = [];
  }

  addFlavor(flavor) {
    this.favoriteFlavors.push(flavor);
  }
} 
```

### IceCreamFlavorNode

这个类将接受一个参数:冰淇淋的味道。这将作为它的标识符。

这个类不需要包含任何方法，因为这是一个无向图，数据从人流向口味，而不是反向。

类定义如下:

```
class IceCreamFlavorNode {
  constructor(flavor) {
    this.flavor = flavor;
  }
} 
```

### 图表

`Graph`类不会接受任何参数，但是它的构造函数将包含三个属性:

*   `peopleNodes`:person nodes 数组。
*   `iceCreamFlavorNodes`:IceCreamFlavorNodes 数组
*   `edges`:包含 PersonNodes 和 IceCreamFlavorNodes 之间的边的数组。

Graph 类将包含六个方法:

*   `addPersonNode(name)`:接受一个参数，一个人的名字，用这个名字创建一个新的`PersonNode`，并把它推到`peopleNodes`数组中。
*   `addIceCreamFlavorNode(flavor)`:接受一个参数，一种冰淇淋口味，用这种口味创建一个新的`IceCreamFlavorNode`，并将其推入`iceCreamFlavorNodes`数组。
*   接受一个参数，一个人的名字。并返回该人的节点。
*   接受一个参数，一种冰淇淋口味。并返回该风味的节点。
*   `addEdge(personName, flavorName)`:接受两个参数，一个人的名字和一种冰淇淋口味，检索这两个节点，将口味添加到人的`favoriteFlavors`数组，并将边推送到 edges 数组。
*   `print()`:简单地打印出`peopleNodes`数组中的每个人以及他们喜欢的冰淇淋口味。

类定义如下:

```
class Graph {
  constructor() {
    this.peopleNodes = [];
    this.iceCreamFlavorNodes = [];
    this.edges = [];
  }

  addPersonNode(name) {
    this.peopleNodes.push(new PersonNode(name));
  }

  addIceCreamFlavorNode(flavor) {
    this.iceCreamFlavorNodes.push(new IceCreamFlavorNode(flavor));
  }

  getPerson(name) {
    return this.peopleNodes.find(person => person.name === name);
  }

  getFlavor(flavor) {
    return this.iceCreamFlavorNodes.find(flavor => flavor === flavor);
  }

  addEdge(personName, flavorName) {
    const person = this.getPerson(personName);
    const flavor = this.getFlavor(flavorName);
    person.addFlavor(flavor);
    this.edges.push(`${personName} - ${flavorName}`);
  }

  print() {
    return this.peopleNodes.map(({ name, favoriteFlavors }) => {
      return `${name} => ${favoriteFlavors.map(flavor => `${flavor.flavor},`).join('  ')}`;
    }).join('\n')
  }
} 
```

# 可视化数据

现在我们有了三个类，我们可以添加一些数据并进行测试:

```
const graph = new Graph(true);
graph.addPersonNode('Emma');
graph.addPersonNode('Kai');
graph.addPersonNode('Sarah');
graph.addPersonNode('Maranda');
graph.addIceCreamFlavorNode('Chocolate Chip');
graph.addIceCreamFlavorNode('Strawberry');
graph.addIceCreamFlavorNode('Cookie Dough');
graph.addIceCreamFlavorNode('Vanilla');
graph.addIceCreamFlavorNode('Pistachio');

graph.addEdge('Emma', 'Chocolate Chip');
graph.addEdge('Emma', 'Cookie Dough');
graph.addEdge('Emma', 'Vanilla');
graph.addEdge('Kai', 'Vanilla');
graph.addEdge('Kai', 'Strawberry');
graph.addEdge('Kai', 'Cookie Dough');
graph.addEdge('Kai', 'Chocolate Chip');
graph.addEdge('Kai', 'Pistachio');
graph.addEdge('Maranda', 'Vanilla');
graph.addEdge('Maranda', 'Cookie Dough');
graph.addEdge('Sarah', 'Strawberry');

console.log(graph.print()); 
```

下面是我们的有向图:

[![Directed graph](img/e1bfa3a02dc1580b0284efa0decf7fba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KyRdpfNq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A8w3ZPB-SyPCszp4errksnQ.png)

* * *

如果你想看到完整的代码，请查看我的 [CodePen](https://codepen.io/emmawedekind/pen/mYJPbO?editors=0011) 。