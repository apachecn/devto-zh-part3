# 保持您的类型和接口更接近(在 TypeScript 中)

> 原文：<https://dev.to/antjanus/keep-your-types-and-interfaces-closer-in-typescript-497i>

我写生产打字稿已经有一年多了，作为业余爱好也有几年了。如果你以前从未使用过 TypeScript，描述它的快速方法是它是 ES2015+和类型一起抛出的。即。具有真实类型的现代 JavaScript。

TypeScript 很棒，我喜欢写它，随着时间的推移，我注意到我自己的风格和模式出现了，我想分享其中一个，并希望证明我为什么坚持这些模式。

## 本地接口>全局接口

TypeScript 中的接口本质上是对象定义，它描述了对象最低限度应该是什么样子。例如，如果我有一个`DatabaseConfig`界面，它可能看起来像这样:

```
interface DatabaseConfig {
  host: string,
  port: number,
  password: string
}

function connectToDb(dbConfig: DatabaseConfig) {
  // database connection config
} 
```

Enter fullscreen mode Exit fullscreen mode

这基本上意味着无论何时调用函数`connectToDb`，都需要传入一个看起来像`DatabaseConfig`接口的对象(以及其属性的适当类型)。

我从 Golang styleguide 的一篇文章(我不记得是哪篇了)中获得的一个模式是“本地接口”的概念，这种接口准确地描述了我需要从单个文件中的一个对象获得什么。

这个`DatabaseConfig`接口，如果共享的话，将呈指数增长，以包含可能接触到这个对象的每个函数的需求。一个`createDatabasePool`函数可能会额外寻找那个配置上的一个`poolSize`属性，现在引用这个接口的每个函数都需要这个属性，不管它们是否使用它。想象一下，我们也有一个函数可以返回特定数据库的驱动程序，所以我们可能需要一个`type`属性，除了驱动程序之外，没有函数关心这个属性。

基本上，共享接口(或者使用我称之为`global interfaces`的东西)会导致接口膨胀，并对引用接口的函数/代码块/任何东西都可能不使用的属性强加人为的要求。它在可能不相关的代码片段之间创建了一个奇怪的“耦合”。

相反，我建议编写文件的本地接口，这些接口只描述文件中的代码在对象中所需的必要属性。如果你有一个`createPool`函数，你可以这样写:

```
interface PoolConfig {
  poolSize: number
}

export function createPool(config: PoolConfig, driver) {
  // uses config.poolSize somewhere in the code
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们告诉在该文件中工作的开发人员，我们真正需要的是`poolSize`，并且我们不使用该配置对象中的任何其他内容。

我发现这非常有用，因为它符合类型实际上只是计算机也可以查看和利用的文档的想法。

## 异常情况

这条规则有几个例外。

例外情况是，如果您对数据使用对象模型，您可能希望将这些模型作为接口提供给开发人员(和编译器),告诉他们您确实需要这个模型。

您可能不关心确切的键，您可能更关心获得实际的模型(或者具有完全相同形状的东西)。

该规则的另一个例外是，如果您有复杂的对象，需要保持其复杂的形状。想象你有一个嵌套 5 层深的物体。更谨慎的做法是导入一个描述这一点的接口，而不是写出无用的、复杂的嵌套接口。