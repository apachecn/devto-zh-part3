# 动作 3:从动作 3 开始

> 原文：<https://dev.to/oieduardorabelo/svelte-3-comecando-com-svelte-3-2em3>

## 网络增强的 web 应用

> 又来了！我又学会了爱！还是只是激情？现在说还为时过早，但我对[快点](https://svelte.dev)发展了强烈的感情，时间会告诉他是否会变成完整的关系。

# O queésvelite？

speedo 是一个用户界面框架，它借用了一些同事(如 React 和 vue . js)的许多或所有伟大想法，但它为桌面带来了自己有趣的方法。此外，它并非完全是一个我们已经习惯的框架，也许更好地被看作是一个编译器。

当您构建用于生产的快速应用程序时，您的代码将以纯 JavaScript 编译，并且经过优化，没有 mix 中包含的其他框架依赖项，从而生成真正较小的包。因此，在使用所有的语法细微之处和易用性进行开发时，它看起来就像一个框架，但在代码编译到生产环境中后，该框架就会消失。两个世界中最好的！

急流已经存在了几年，但是随着[的到来](https://svelte.dev/blog/svelte-3-rethinking-reactivity)的速度提高 3，开发人员可以获得更强大的功能。就我个人而言，在选择用于构建 web 用户界面的工具时，我感觉到一个真正的竞争对手正在崛起。

* * *

不要再聊天了，让我们把一个‘t0’敲在引擎盖上。。如果你对更广泛的概述和早期的故事感兴趣，我建议你观看这个来自‘T2’rich Harris 的演讲，快讯的创造者:

[https://www.youtube.com/watch?v=AdNJ3fydeao](https://www.youtube.com/watch?v=AdNJ3fydeao)

# 启动快速项目 3

从快捷开始，使用创建初始项目结构的 [degit](https://www.npmjs.com/package/degit) 软件包。您可以在命令行中通过调用“**deg**com[npx](https://alligator.io/workflow/npx)来执行此操作。让我们创建一个简单的应用程序:

```
$ npx degit sveltejs/template nosso-exemplo
$ cd nosso-exemplo
$ npm install 
```

并且有了它，现在可以运行一个开发服务器，使用:

```
$ npm run dev 
```

我们准备好开始了！你可以访问地址**[【http://localhost:5000](http://localhost:5000)**查看你的申请！

# 部分快速应用程序

我喜欢知道所有的文件是如何适应一个项目的，很快就没有什么不同了，为了了解整体的情况，让我们打破东西:

# Nosso package.json

在您最喜欢的代码编辑器中打开项目，然后访问文件“**”package . JSON“**”。注意怎么只有**和**没有【依赖】**！你在做完全不同的事情。不存在常规依赖关系，因为框架在构建用于生产时会将其代码编译为纯 JavaScript。**

 **# Ponto de entrada JavaScript:src/main . js

如果你看文件夹 **src** ，你会看到一个根组件 **App。快点**和 **main.js** ，这是我们的切入点。与 React 或 vue 一样，快速应用程序是使用组件创建的，并且通常会有一个 App 组件作为应用程序组件树的根组件。 **main.js** 只需使用存在于 **public/index.html** 中的目标元素来获取此根组件和实例，然后传递**propes**的初始，这可能是可选的。

同样，就像反应或视图中的部件一样，快速部件可以接收到“**propis**。

# 一种“大众面食”

在开发过程中，**public**文件夹将包含一个未优化的**版本的应用程序，并将其打包到**index . html**中，软件包将在其上运行。**

# 档案" .快点"

快速组件是使用扩展名“**”编写的。快速“**”包含组件的所有逻辑、样式和标记。如果你是从 vista 世界来的话，你已经习惯了这样的事情。

一个典型的快速文件看起来像:

```
// MeuComponente.svelte
<script>
  // lógica do seu componente
</script>

<style>
  /* os estilos para sua marcação */
</style>

<!-- A marcação HTML do seu componente --> 
```

想到您只需编写 JavaScript、CSS 和 HTML 就能使人耳目一新！唯一的区别是，flash 在 HTML 中添加了一些特定的语法，以及处理 JavaScript 的一些特殊规则。

啊，而且样式将被隔离到快速文件中描述的组件，所以不需要使用复杂的类或者担心组件之外的特殊性。

# 构造构件

我们将创建一个组件**，并学习一些速动的具体语法。这里是我们的起点:** 

```
$ touch src/‌Counter.svelte 
```

```
<script>
  let count = 0;
</script>

<div>Current count: {count}</div> 
```

标记中的键用于补间值。

# Adicionando eventos

我们将添加一些按钮来增加或减少计数器:

```
<script>
  let count = 0;

  function increment() {
    count += 1;
  }

  function decrement() {
    count -= 1;
  }
</script>

<div>Current count: {count}</div>

<button on:click={increment}>Increment</button>
<button on:click={decrement}>Decrement</button> 
```

如您所见，您可以使用语法**on:event name**来连结事件。然后可以传递对 JavaScript 逻辑中定义的函数的引用。

# 有条件

您可以使用特殊语法**if**来使用条件。仅当我们的计数器值大于 5 或小于-5 时，我们才会显示一些段落:

```
<script>
  let count = 0;

  function increment() {
    count += 1;
  }

  function decrement() {
    count -= 1;
  }
</script>

<div>Current count: {count}</div>

{#if count > 5}
  <p>⚠️ That's a little much, don't you think!</p>
{:else if count < -5}
  <p>👻 How low can you go!</p>
{/if}

<button on:click={increment}>Increment</button>
<button on:click={decrement}>Decrement</button> 
```

子句:else if 是可选的，您也可以使用一个: **else** 。

# 链接双向值

有时，您可能希望以两种方式链接值，例如，表单条目必须从其组件中的变量获取其值，但更改其值也必须更改该变量的值。这是很容易做到这一点与快速！我们将在表单条目中显示我们的计数，并允许用户手动设置当前计数:

```
<script>
  let count = 0;

  function increment() {
    count += 1;
  }

  function decrement() {
    count -= 1;
  }
</script>

<div>Current count: <input bind:value={count} type="number" /></div>

{#if count > 10}
  <p>⚠️ That's a little much, don't you think!</p>
{:else if count < -10}
  <p>👻 How low can you go!</p>
{/if}

<button on:click={increment}>Increment</button>
<button on:click={decrement}>Decrement</button> 
```

双向连接使用语法 **bind:value={myVal}** 进行。在此请注意，我们使用了**输入**型**号码**并自动解开，将输入值转换为**号码**型，而不是通常的**字串**形式输入提供。一条很好的捷径！

# 编译生产用

准备好将应用程序投入生产时，只需运行命令 **build** :

```
$ npm run build 
```

而现在文件夹 **public** 将会有一个**版本的应用程序经过挖掘、优化和打包**，准备发送到您附近的 CDN！👍

# 学习更多

这篇帖子几乎没有刮到表面，我打算开始一系列的探索急流的诅咒。

与此同时，看看官方教程[和](https://svelte.dev/examples)示例，这两个都是学习速度方面的知识的绝佳资源。

我打赌你很快就会觉得超有生产力！

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   [苗条 3](https://alligator.io/svelte/getting-started-with-svelte) 入门，escrito originate por[@ alligatorio](https://mobile.twitter.com/alligatorio)****