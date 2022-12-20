# 快速 3:反应性简介

> [https://dev . to/oieduardorabelo/speedo-3-introduction Cao-a-reactive-5b9a](https://dev.to/oieduardorabelo/svelte-3-introducao-a-reatividade-5b9a)

## 网络增强的 web 应用

> 我们谈到了“T0”从 fast 3 开始的第一步，但在最初的帖子中，我有意省略了 fast 最重要的功能之一:反应性。

响应性是近年来使用 JavaScript 的现代用户界面结构的最新时尚。反应度表示变更的值会自动反映在 DOM 中。

角度允许通过 rxjs 和 as 观察到的响应编程，而 vue . js 允许通过 [**【购买的属性】**](https://alligator.io/vuejs/computed-properties) 被动地重新计算值。很快，它使用了一种不太为人所知的 JavaScript 功能，叫做 [**【标签】**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label) ，允许**和**反应条件。这意味着您可能具有某些值，这些值会在其他值更改时自动重新计算。这真的很厉害，而且你会看到，快速方便了这项任务。🥧****

 ****# 创建字数统计

让我们来看看快速反应如何通过构建一个单词计数器示例。

这里是我们的组成部分:

```
$ touch src/WordCounter.js 
```

并放入:

```
<script>
    let text = '';
</script>

<style>
  textarea {
    width: 100%;
    background: aliceblue;
    font-size: 2rem;
  }
</style>

<textarea bind:value={text} rows="10" /> 
```

这里没什么特别的，除了 **text** 的值和 **textarea** 的值之间的双向联系。

# 反应性声明

现在，我们将添加一个反应语句，以便在变量 **text** 的值更改时自动计算字数和字符数:

```
<script>
  let text = '';

  $: characters = text.length;
  $: words = text.split('  ').length;
</script>

<style>
  textarea {
    width: 100%;
    background: aliceblue;
    font-size: 2rem;
  }
</style>

<textarea bind:value={text} rows="10" />

<p>Character Count: {characters}</p>
<p>Word Count: {words}</p> 
```

我们声明了两个新变量:字符和**字**，它们根据内部值 **text** 计算值，并将自动重新计算。

> **t1】部分:是一个**标签**在 JavaScript 中完全有效。您可能以前没有在 JavaScript 中使用过“**标签”**，它们用于带有“**嵌套循环”**的极端情况。快速给标有标签的语句赋予特殊含义，并自动检测语句的响应性。**

# 反应条件

此使用标签语法的响应性不仅适用于声明新变量，而且还可用于在值发生更改时动态执行语句。

当控制台更改时，我们会将值 **text** 记录到控制台中:

```
<script>
  let text = "";

  $: characters = text.length;
  $: words = text.split("  ").length;

  $: console.log("your text:", text);
</script>

<style>
  textarea {
    width: 100%;
    background: aliceblue;
    font-size: 2rem;
  }
</style>

<textarea bind:value={text} rows="10" />

<p>Character Count: {characters}</p>
<p>Word Count: {words}</p> 
```

想象一下这对于调试应用程序有多有用！

#### 多个语句

可以使用键将多个语句组合成一个块:

```
$: {
  console.log("---");
  console.log("your text:", text);
} 
```

#### 条件语句

而且你甚至可以用条件作为你的陈述:

```
$: if (text.toLowerCase().includes("see you later alligator")) {
  console.log("Not so soon baboon!");
  text = "";
} 
```

现在，每当我们的变量 **text** 包含*string***【see you la ter 鳄鱼】**，控制台就会记录一条消息，我们重置了变量 **text** 的值。

这样，您就可以继续运行并使您的应用程序快速响应！

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   [苗条反应性介绍](https://alligator.io/svelte/reactivity-intro)，escrito originate por[@ alligatorio](https://mobile.twitter.com/alligatorio)****