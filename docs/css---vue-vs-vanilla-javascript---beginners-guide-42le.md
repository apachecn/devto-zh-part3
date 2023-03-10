# Vue 与传统 CSS -初学者指南

> 原文：<https://dev.to/michi/css---vue-vs-vanilla-javascript---beginners-guide-42le>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/vue-vs-traditional-css)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

#### 这是比较传统前端 web 开发和 VueJs 的系列文章的第二部分。如果你还没有看过《T2》第一部，一定要去看看。今天我们要来看看 CSS！

* * *

对于传统的网站，我们有很多方法可以实现 CSS。最常见的方法是将样式放在它自己的 CSS 文件中。

通常有一个全局 CSS 文件，然后每页一个 CSS 文件。对于较小的页面，你也会经常在 HTML 页面中找到 CSS(通常在`<head>`部分)。

这往往会产生很大的 CSS 文件，很难在不破坏某些东西的情况下进行管理或重构。

### 视图

在 Vue 中，每个组件都可以有自己的样式，即**作用于组件的**。

```
<template>
<div class="success-message">this will be green</div>
</template>

<style scoped>
.success-message {
    color: green;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

注意我们传递给`<style>`标签的`scoped`属性。没有这一点，CSS 将在全球范围内应用。我们想要避免的事情。因此，如果我继续创建另一个组件，它有一个使用了类`success-message`的 div，这个 div 不会变成绿色。

我们在 Vue 中最终得到的是一堆小组件，每个组件中只有很少甚至没有 CSS。不得不以某种方式组织一大套全球 CSS，处理相互冲突的样式规则和特殊性的日子已经一去不复返了。加上一个 CSS 框架(见下文),你将得到一个容易理解的小 CSS！

它还避免了经常在 JavaScript、HTML 和 CSS 文件之间切换，因为所有内容都在一个文件中。

对于你写的 CSS，我仍然建议遵循 BEM 这样的方法。

## 与班级打交道

我们经常需要在元素中添加和删除类来应用特定的样式。

### 繁体

```
function handleClick() {
    const messageEl = document.getElementById('message')
    messageEl.classList.add('primary')
}
// ...
// ...
function deleteProject() {
    const messageEl = document.getElementById('message')
    messageEl.classList.remove('primary')
    messageEl.classList.add('danger')
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，类列表可能在应用程序的任何地方被修改，并且很难跟踪。

### 视图

在 Vue 中有多种方式:

##### 使用数组

```
<template>
<div :class="['consistent-rule', isPrimary ? 'primary' : '']">message</div>
</template>

<script>
export default {
    data() {
        return {
            isPrimary: true,
        }
    }
}
</script>

<style scoped>
.primary {
    color: #369369;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

##### 使用对象

```
<template>
<div :class={'consistent-rule': true, 'primary': isPrimary}">message</div>
</template>

<script>
export default {
    data() {
        return {
            isPrimary: true,
        }
    }
}
</script>

<style scoped>
.primary {
    color: #369369;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

方便的是所有可能的类都在一个地方。当然，您也可以将`class`提取到一个计算字段中。

## 使用 SCSS(或其他前置处理器)(跳过设置)

### 繁体

这通常需要您创建一个`.scss`文件，该文件将被编译成`public`或`dist`目录中的`.css`文件，然后您必须导入该文件。您将不再能够将 CSS 和 HTML 放在同一个文件中。

### 视图

请看看这个

```
<template>
<a class="red--text" href="#">this will be red</a>
</template>

<style scoped lang="scss">
$alert: #c04848;
.red--text {
    color: $alert;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

唯一需要做的更改是将`lang="scss"`添加到`<style>`标签中。

## 使用框架

我们来对比两个材质设计框架。普通 JavaScript/HTML 的实体化和 VueJs 的 Vuetify。

### 实体化

CSS 框架通常就是那样，CSS 框架。例如，如果你想要一个分页，你必须考虑你实际上想要显示多少列表项。

```
<ul class="pagination">
    <li class="disabled"><a href="#!"><i class="material-icons">chevron_left</i></a></li>
    <li class="active"><a href="#!">1</a></li>
    <li class="waves-effect"><a href="#!">2</a></li>
    <li class="waves-effect"><a href="#!">3</a></li>
    <li class="waves-effect"><a href="#!">4</a></li>
    <li class="waves-effect"><a href="#!">5</a></li>
    <li class="waves-effect"><a href="#!">6</a></li>
    <li class="waves-effect"><a href="#!"><i class="material-icons">chevron_right</i></a></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

现在，当数据改变时，你必须添加/删除`<li>`元素，这会变得非常混乱。

### 消失

当然，随着用户界面与数据的同步，可以让你的生活变得容易得多。

```
<template>
<v-pagination
    v-model="page"
    :length="6"
></v-pagination>
</template>

<script>
export default {
    data() {
        return {
            page: 1,
        }
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这也避免了许多组件的所有这些`data-`属性。

* * *

Vuetify 还使整个 HTML 更具可读性。
我们来看看比较流行的`cards`。

### 实体化

```
<div class="card blue-grey darken-1">
    <div class="card-content white-text">
        <span class="card-title">Card Title</span>
        <p>I am a very simple card. I am good at containing small bits of information.
        I am convenient because I require little markup to use effectively.</p>
    </div>
    <div class="card-action">
        <a href="#">This is a link</a>
        <a href="#">This is a link</a>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 消失

```
<template>
<v-card class="blue-grey darken-1 white--text">
    <v-card-title>
        <h3 class="headline">Card Title</h3>
        <div>I am a very simple card. I am good at containing small bits of information. 
        I am convenient because I require little markup to use effectively.</div>
    </v-card-title>
    <v-card-actions>
        <v-btn flat color="orange">This is a link</v-btn>
        <v-btn flat color="orange">This is a link</v-btn>
    </v-card-actions>
</v-card>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我不知道你怎么想，但我发现 Vue 解决方案更具表现力。我马上就能看到我们正在处理的是哪种元素(例如`<v-card-actions>`)，而不必在类列表中搜索它。

## 结论

我不得不再次把它交给 Vue，让代码再次变得更可读，所有这些都没有任何复杂的语法，比如

```
styled.a`
  padding: 0.5rem 0; ${props => props.primary && css`
    background: white;
    color: palevioletred;
  `} ` 
```

Enter fullscreen mode Exit fullscreen mode

在你的组件中只有很少甚至没有 CSS，因此在处理组件时只需要担心很少的 CSS，这真的令人耳目一新！

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。