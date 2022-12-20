# 给 Vue 开发者的提示:-避免直接操作 DOM

> 原文：<https://dev.to/therealdanvega/tips-for-vue-developers-avoid-directly-manipulating-the-dom-19mn>

我今天和一个学生一起做最后的顶点项目，他们在数据绑定方面遇到了一些问题。当我们开始研究代码时，有一件事引起了我的注意，那就是一面大红旗。有一个带有 id 的复选框，然后有一个变更事件处理程序`toggleStatus()`

```
<input type="checkbox" id="thecheckbox" @change="toggleStatus" /> 
```

在我们找到问题的根源之前，我看到了一些类似这样的代码。

```
const isChecked = document.getElementById("thecheckbox"); 
```

当你使用 Vue 时，你必须忘记你可以访问 DOM。我这样说的原因是，如果你直接访问 DOM，可能有更好的方法。我想澄清的是，你没有违反这里的任何规则，没有人会因此对你大喊大叫，但我们应该从另一个角度考虑这个问题。

在我们的 toggle status 方法中，假设您需要知道复选框是否被选中，以确定要采取的操作。在任何应用程序中，这都是一个非常常见的过程。

```
methods: {
  toggleStatus() {
    const isChecked = document.getElementById('thecheckbox').checked;
    if( isChecked ) {
      // do something
    } else {
      // do something else
    }
  }
} 
```

### 更好的方法

更好的方法是理解这个组件的每个实例都有自己的状态。这意味着我们可以将组件中的控件绑定到我们的数据。我将重构我们之前的例子，首先创建一个名为 isChecked 的变量，并将其默认为 false。这是因为默认情况下，我不想选中这个复选框。

```
data() {
  return {
    isChecked = false
  }
} 
```

现在我们有了复选框的默认状态，我们可以在组件中使用它。您可以将复选框 checked 属性绑定到变量`isChecked`。当组件第一次加载时，它将被取消选中，但现在被绑定到我们的变量，所以每当它改变我们的复选框将被更新。

```
<input type="checkbox" :checked="isChecked" @change="toggleStatus" /> 
```

现在，在我们的切换状态方法中，我们可以在 if 表达式中使用组件数据。我们还可以通过将复选框设置为当前状态的相反状态来翻转它的状态。

```
methods: {
  toggleStatus() {
    if(this.isChecked) {
      // do sommething
    } else {
      // do something else
    }
    this.isChecked = !this.isChecked;
  }
} 
```

## 使用$refs 访问 DOM

如果您绝对需要访问 DOM 怎么办？有些情况下，您可能需要引用元素来执行某种类型的操作。我在使用第三方组件和父/子组件时看到过这种情况。

我将从一个非常简单的例子开始，但是在这个例子中，你不应该使用\$refs。假设您的组件中有一个按钮，您想访问它以便更改文本。您可以为按钮分配一个 ref 属性，然后使用`$refs`对象访问它。

```
<button ref="myButton">My Button</button> 
```

```
methods: {
  onButtonClick() {
    const btn = this.$refs.myButton;
    btn.innerText = 'New Button Text'
  }
} 
```

## 在 Vue 中使用$refs 的实际例子

同样，这不是最实际的例子，因为我们只是在做和前面例子中一样的事情。正如我前面说过的，当您使用父/子组件时，您可能会遇到这个问题。在这个例子中，假设您有一个结帐表单，在这个组件中，您有一个名为`CustomerForm.vue`的子组件。

在我们的结帐表单组件中，我们希望以编程方式设置客户表单组件中输入的焦点。我们可以这样做方法是在我们的 checkout 表单中为我们的 customer 表单分配一个引用。

```
<template>
  <div id="checkout">
    <customer-form ref="customer" />
  </div>
</template> 
```

在客户表单中，我们将为名字输入框分配一个 ref。

```
<template>
  <div id="customer-form">
    First Name:
    <input type="text" placeholder="Enter your first name" ref="firstName" />
  </div>
</template> 
```

现在，在我们的 mounted 方法的 checkout 表单中，我们可以将注意力集中在 customer 表单的 first name 输入上。

```
<template>
  <div id="checkout">
    <customer-form ref="customer" />
  </div>
</template>

<script>
import CustomerForm from "@/components/CustomerForm";

export default {
  name: "checkout-form",
  components: {
    CustomerForm
  },
  mounted() {
    this.$refs.customer.$refs.firstName.focus();
  }
};
</script> 
```

您需要注意的只是来自 [Vue 文档](https://vuejs.org/v2/guide/components-edge-cases.html#Accessing-Child-Component-Instances-amp-Child-Elements)的一个小提示。

> $refs 仅在组件渲染后填充，它们不是反应性的。它只是作为直接子操作的出口——您应该避免从模板或计算属性中访问$refs。

## 结论

我想强调的是，在 Vue 应用程序中访问 DOM 并没有做错什么。我只是认为，在大多数情况下，有一个更好的方法，我希望这个例子能够表明这一点。如果你知道一个好的直接操作 DOM 的用例，请联系我并告诉我。一如既往...

快乐编码

丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*