# 如何利用道具和数据

> 原文：<https://dev.to/proticm/how-to-vue---props-vs-data-o41>

Vue 提供了两种存储变量的方式。一种方式是使用**道具**，另一种方式是使用**数据**。它们都可以在 Vue 实例和组件上定义。

起初，它们可能看起来令人困惑，因为它们非常相似，似乎服务于相似的目的。

在这篇文章中，我将试着区分它们，希望它们的区别会更明显。

# 道具

Props 是我们将数据从一个父组件向下传递给它的任何子组件的方法。

当我们用 Vue 构建一个应用时，我们是以一种分层的方式来做的。这是什么意思？这意味着，从 Vue 实例开始，数据自上而下流动，在父节点上创建的每个数据变量都可以通过属性传递给其子节点。

props 可以定义为一个字符串(名称)数组，但这不包括传递给它的值的验证。如果需要验证，需要列出一个带有特定配置的对象。

此外，需要指出的是，我们可以向每个属性传递一个动态或静态值。

道具作为数组:

```
export default {
  name: 'my-component',
  props: ['name', 'surname']
} 
```

道具作为对象:

```
export default {
  name: 'my-component',
  props: {
    name: String, // must be a string
    surname: String, // must be a string
    propA: [String, Number], // allows multiple types
    propB: { // required string
      type: String,
      required: true
    },
    propC: {
      type: Number,
      default: 15 // the default value
    },
    propD: {
      validator: function (value) { // custom validator function
        return true
      }
    }
  }
} 
```

如果我们在开发模式下运行 Vue，每次当一个道具类型无效时，它都会抛出一个警告。在生产模式下，情况并非如此。

好了，现在当我们知道如何定义道具时，我们应该看看如何给它传递一个值。这是通过模板完成的。

模板:

```
<template>
  <div>
    Name: {{name}}
    Surname: {{surname}}
  </div>
</template> 
```

我们可以传递一个静态值:

```
<my-component name="John" surname="Doe"></my-component> 
```

或者，如果需要动态值，我们需要使用`v-bind`指令:

```
<my-component v-bind:name="dynamicName" v-bind:surname="dynamicSurname"></my-component> 
```

请注意，骆驼外壳的属性需要使用烤肉串外壳的等价属性。然而，如果使用字符串模板，这种限制就不适用。

说了这么多之后，我们应该指出尊重**单向数据流**的重要性。

> 所有的属性在子属性和父属性之间形成一个单向向下的绑定:当父属性更新时，它将向下流向子属性，而不是相反。

这意味着我们不需要担心我们的子组件属性是否有来自父组件的最新值。道具是反应性的，这都是为我们做的。Vue 将确保在更改父值后每个属性都已更新。如果我们试图自己更新子值，框架会在控制台中抛出警告。

# 数据

数据是应用程序中每个组件的私有内存，你可以在其中存储它需要的任何变量。

> 数据对象类似于组件状态的模式

换句话说，我们应该将数据配置用于将被相应组件使用的变量**。如果我们需要将这些数据传递给另一个组件，我们需要使用 props 将其传递给子组件，或者使用 events 将其传递给父组件。**

我们的数据**应该被配置为一个函数**，它返回一个对象，其中每个属性代表一个变量，该变量将在我们的模板中可用。通过将数据属性定义为函数，我们可以确保组件的每个实例都有自己的数据对象副本。

```
<template>
    <span>{{message}}</span>
</template> 
```

```
export default {
  name: 'my-component',
  data() {
    return {
      message: 'Hello!'
    }
  }
} 
```

在上面的例子中，如果`message`属性没有被定义为一个数据变量，框架会抛出一个警告，告诉我们正在试图使用一个不存在的东西。

在`data`对象中定义的每个变量将会被**反应**。这意味着，就像在 props 示例中一样，我们不需要担心值和模板更新。每当我们的应用程序中发生相应的变化时，框架就会处理这个问题。

基本上，只要我们正在更新一个反应性属性(属性、计算属性和数据中的任何东西)，框架就会为我们处理更新。

# 包装完毕

虽然 props 和 data 是相似的，但是在这一点上的区别应该更加清楚，它们应该一起使用，以便在我们的应用程序中构建适当的数据流。

我认为这一行指出了关键的定义:

数据和道具都是反应性的，但数据更像是“我出生时获得的自己的特定基因”，道具则是“我希望从父母那里获得这些基因”

感谢阅读，下一篇文章再见。