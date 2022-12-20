# 破窗理论&童子军规则

> 原文：<https://dev.to/lucasheim/broken-window-theory-boy-scout-rule-pop>

2000 年，作者安德鲁·亨特和戴维·托马斯在一本被认为是当今任何开发人员的“必备”书籍《实用程序员:从熟练工到大师》中，谈到了应用于软件编程的“破窗理论”。这一理论表明，建筑物(或软件，在我们的例子中)等事物的贬值受到熵的影响，熵是一个物理术语，也可以被描述为系统中的“无序”，并且它似乎总是在升级。所以，如果你打破了一栋建筑的窗户，人们倾向于认为这是废弃的，可能会破坏它或误用它。软件开发中的项目也是如此:如果你总是到处留下糟糕的代码或实践，人们会认为这是可以接受的，雪球越滚越大，直到你的整个代码库都被感染。

一个很大的例子是部分代码缺乏测试。我们中的大多数人都在紧张的期限内工作，被迫尽快交付，有时这是你冲刺的最后一天，客户在等待一个新特性，而你最终没有创建单元测试就交付了它。为了通过您的 CI 运行的覆盖测试，您在这个新类中放置一个 ignore 标志，一切正常。

比方说，下一个 sprint，你的一个同事需要增加那个类的功能。她开发了它，运行了一些手工测试，一切似乎都正常。她创建了一个拉取请求，而你的 CI 系统没有抱怨测试或覆盖。这很奇怪，但忽略标志在那里，她可能认为这个类没有单元测试是有原因的，雪球越滚越大。

另一个例子是大系统中固有的代码复制粘贴。有人用错误的方式做一件事，使用错误的结构或抽象，当其他同事需要用类似的逻辑做一件事时，他会用同样的方式做，将不好的做法传播到代码的几个部分。

举个例子，假设我们有这样的代码:

```
public persistItems(): Promise<any> {
  const items = this.getItems();
  for (const item of items) {
    if (!Boolean(item.field1) && !item.field3) {
      return service.createItem(item);
    } else if (Boolean(item.field1) && item.field3) {
      return service.deleteItem(item);
    } else if (item.field7) {
      return service.updateItem(item);
    }
  }
} 
```

这段代码有很大的改进空间。所有这些嵌套在 *for* 循环中的条件都不理想，它们并没有真正说明正在测试什么，因为它们使用了一些数据库命名(field1、field3、field7)。这是一个破碎的窗口，因为需要在这里改变条件或添加新状态的人很可能只是添加一个新的 *if* 或一个新的布尔条件，这使得理解和维护代码更加困难。

为了改善这一点，我们可以使用一些重构技术。我们将基本上使用[用多态](https://sourcemaking.com/refactoring/replace-conditional-with-polymorphism)替换条件，以允许`persistItems`代码尽可能简单，并将所有条件逻辑封装在一个专门为此设计的工厂中(单一责任原则)。

因此，所有这些条件将只执行一行代码，负责以某种方式持久化项目。所以我们可以为这个持久对象创建一个接口:

```
export interface Persistable {
  persist(): Promise<void>;
} 
```

然后我们可以为我们的每个项目创建一个类，实现这个接口。我只举其中一个例子:

```
export class NewItem implements Persistable {
  constructor(private item) {};

  public persist(): Promise<void> {
    return service.create(this.item);
  }
} 
```

好了，我们已经把我们的行为分成了几个类，但是这还没有解决我们的问题。我们需要创建工厂来封装条件:

```
export class ItemFactory {
  public make(item: Item): Persistable {
    if (this.isNewItem(item)) {
      return new NewItem(item);
    } else if (this.isDeletedItem(item)) {
      return new DeletedItem(item);
    } else if (this.isUpdatedItem(item)) {
      return new UpdatedItem(item);
    }
    return new UnchangedItem(item);
  }

  private isUpdatedItem(item: Item): boolean {
    return item.field7;
  }

  private isDeletedItem(item: Item): boolean {
    return Boolean(item.field1) && item.field3;
  }

  private isNewItem(item: Item): boolean {
    return !Boolean(item.field1) && !item.field3;
  }
} 
```

在这里，我们能够提取每个条件(`isNewItem`，`isUpdatedItem`，...)并使它们对每个人都可读。除此之外，我们引入了一个`UnchangedItem`，它是一个[空对象](https://sourcemaking.com/design_patterns/null_object)，所以如果我们的项目不需要被持久化，它将不会做任何事情。

好了，做完这些之后，让我们看看我们的方法`persistItems`发生了什么变化:

```
public persistItems(): void {
    const items = this.getItems();
    const itemFactory = new ItemFactory();
    for (const item of items) {
      const persistableItem = itemFactory.make(item);
      persistableItem.persist();
    }
  } 
```

对于每一项，我们只需调用`.persist()`，让工厂决定哪个类应该被实例化，每个行为应该被调用。每当有人需要添加一个新类型的项目时，他们都有一个清晰的路径，知道它应该在哪里(ItemFactory ),你可以通过查看那个类来更好地跟踪所有的条件。

有一些方法可以应对破窗理论。首先，你要做好预防工作。如果有不符合项目标准的代码，由于这样或那样的原因，它应该尽快被标记和重构。如果由于截止日期的原因，某些东西需要在没有测试的情况下发布，这是可以的，但是，理想情况下，你下一个 sprint 的第一个任务应该是对其进行单元测试，并防止更多的坏窗口。

其次，你应该应用童子军规则。这一条是说你应该**总是**让你正在工作的代码比你得到它的时候更好。这真的很重要，因为在大多数情况下，重构和质量改进并不是项目经理或客户的首要任务。他们希望产品能够按时交付，并具有工作特性。糟糕的代码主要影响我们这些开发人员，因为它会降低我们的工作速度，使我们的工作变得复杂，所以重构不应该是一项积压工作或 JIRA 的任务，而是在我们工作时整理代码的日常工作。所以无论何时你需要添加一些东西，你发现一个坏了的窗口，首先修复代码，不要试图把你的特性放在一堆乱七八糟的代码里，因为这只会让它越来越混乱。