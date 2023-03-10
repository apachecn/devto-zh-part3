# 用 TypeScript 构建 Vue.js 应用程序-第二部分

> 原文：<https://dev.to/georgehanson/building-vue-js-applications-with-typescript-part-two-2808>

*本文最初发布在我的博客这里-[https://www . Georg Hanson . co . uk/blog/building-vue js-applications-with-typescript-part-two](https://www.georgehanson.co.uk/blog/building-vuejs-applications-with-typescript-part-two)T3】*

前阵子我发了一篇文章，题目是《用 TypeScript 构建 VueJS 应用》。如果你还没看过，一定要看一看。点击此处[即可找到。](https://dev.to/georgehanson/building-vuejs-applications-with-typescript-1j2n)

在那篇文章的结尾，我提到我将写一篇后续文章解释方法、道具、子组件等等。虽然已经有一段时间了，但这是这个系列的第二篇文章。

在这篇文章中，我们将继续我们离开的地方。但是我们将创建一个非常简单的待办事项列表应用程序来演示各种概念。让我们开始吧。

## 设置

我们需要做的第一件事是为我们的待办事项列表创建一个新组件。在您的`src/components`文件夹中，创建一个名为`ToDo.ts`的新文件，并添加以下样板文件。

```
import { Component, Vue } from 'vue-property-decorator';
import WithRender from './to-do.html';

@WithRender
@Component
export default class ToDo extends Vue {

} 
```

另外，在同一个目录中创建一个名为`to-do.html`的新文件。这里有一些非常基本的 HTML 让我们开始。

```
<div>
    <h1>My To-Do App!</h1>
    <form>
        <input type="text" placeholder="Enter task...">
        <button type="submit">Add Task</button>
    </form>
</div> 
```

最后，我们需要更新视图目录中的`Home.vue`文件，这样它就可以加载新的`ToDo.ts`组件。更新组件，如下所示。

```
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <ToDo />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import ToDo from '@/components/ToDo.ts';

@Component({
  components: {
    ToDo,
  },
})
export default class Home extends Vue {}
</script> 
```

## 我们的第一个组件

我想做的第一件事是将我们的表单抽象成它自己的组件。让我们创建一个名为`ToDoForm.ts`的新组件，并复制 HTML 模板。我们应该有两个像这样的新文件。

**ToDoForm.ts**

```
import { Component, Vue } from 'vue-property-decorator';
import WithRender from './to-do-form.html';

@WithRender
@Component
export default class ToDoForm extends Vue {

} 
```

**to-do-form.html**T2】

```
<form>
    <input type="text" placeholder="Enter task...">
    <button type="submit">Add Task</button>
</form> 
```

既然我们已经抽象了组件，我们需要在父组件中包含这个子组件。为此，我们需要将组件导入到 TypeScript 文件中，注册它，然后更新模板以显示它。让我们现在过一遍。首先，将`ToDoForm.ts`组件导入到`ToDo.ts`组件中。

```
import ToDoForm from './ToDoForm'; 
```

接下来，我们需要注册组件。我们可以通过向我们的`@Component`装饰器传递一个对象来做到这一点。在这里，我们可以像配置任何普通 Vue 组件一样配置组件。

```
@Component({
  components: {
    'to-do-form': ToDoForm
  }
}) 
```

我们的`ToDo.ts`文件现在应该是这样的:

```
import { Component, Vue } from 'vue-property-decorator';
import WithRender from './to-do.html';
import ToDoForm from './ToDoForm';

@WithRender
@Component({
  components: {
    'to-do-form': ToDoForm
  }
})
export default class ToDo extends Vue {

} 
```

最后一步是现在更新我们的`to-do.html`模板，以便我们引用子组件。只需删除表单并用一个`<to-do-form />`标签替换它。完成后，我们的模板文件应该如下所示。

```
<div>
    <h1>My To-Do App!</h1>
    <to-do-form />
</div> 
```

您现在应该看到表单显示在我们的页面上。

## 显示任务

接下来我们要处理的事情是向用户显示任务。首先，我们需要将任务存储为一个数组。在我们的`ToDo.ts`组件中，我们将添加一个新的属性。这本质上与在标准的`.vue`组件中添加任何数据属性是一样的。

让我们为任务定义一个类型。在`src`中创建一个`types`文件夹，然后创建一个名为`Task.ts`的文件。我们的任务会相对简单。每个任务将由一个描述和一个完成状态组成。这是我们的类型定义。

```
type Task {
  completed: boolean;
  description: string;
}

export default Task; 
```

现在我们可以在我们的`ToDo.ts`组件中创建我们的数据属性。将类型导入到我们的组件中...

```
import Task from '@/types/Task'; 
```

...然后将以下属性添加到该类中。

```
public tasks: Task[] = []; 
```

为了在页面上看到结果，我们需要使用一个`v-for`循环来呈现它们。这里你可以看到我已经更新了`to-do.html`模板中的模板，以无序列表的形式输出每个任务项。

```
<div>
    <h1>My To-Do App!</h1>
    <to-do-form></to-do-form>
    <ul>
        <li v-for="task in tasks"><input type="checkbox" :checked="task.completed"> {{ task.description }}</li>
    </ul>
</div> 
```

现在，让我们对一些任务进行硬编码，这样我们就知道它正在工作。默认情况下，更新`ToDo.ts`组件中的 tasks 属性，使数组中包含一些项目。

```
public tasks: Task[] = [
  { description: 'Make Coffee', completed: false },
  { description: 'Feed Dragons', completed: false },
]; 
```

现在，您应该可以在页面上看到这些任务。干得好！

## 创建新任务

所以我们现在有了一个在页面上显示当前任务的表单和方法。接下来，我们需要实际添加功能，以便当用户在表单中添加任务时，它会更新数据属性。

为此，我们首先需要编辑我们的`ToDoForm.ts`组件。我们需要使用`v-model`,这样我们就可以通过数据绑定捕获输入。在您的`ToDoForm.ts`文件中，添加一个名为 task 的新属性。

```
public task: string = ''; 
```

现在更新`to-do-form.html`模板，这样输入字段就有了`v-model`。

```
<form>
    <input type="text" placeholder="Enter task..." v-model="task">
    <button type="submit">Add Task</button>
</form> 
```

太好了，我们现在有了捕捉用户输入的方法。接下来，我们需要确保当单击“添加任务”按钮时，我们向父组件发出一个事件。让我们向模板中的表单标记添加一个事件侦听器。

```
<form @submit.prevent="emitTask">
    <input type="text" placeholder="Enter task..." v-model="task">
    <button type="submit">Add Task</button>
</form> 
```

接下来，我们需要在我们的`ToDoForm.ts`组件中创建`emitTask`方法。只需在类中添加一个新方法。在这个方法中，我们希望发出一个新事件，传递在表单中输入的值。然后，我们希望为下一次输入重置该值。

```
public emitTask(): void {
  this.$emit('added', this.task);
  this.task = '';
} 
```

现在我们有了一个事件发射，我们可以在父组件中监听这个事件。首先，让我们向我们的`to-do.html`模板文件中的组件添加一个事件监听器。

```
<to-do-form @added="addTask" /> 
```

现在我们需要创建监听器。向名为`addTask`的`ToDo.ts`类添加一个新方法。在这个方法中，我们希望将一个新的项目和来自事件的描述一起推送到 tasks 属性。

```
public addTask(description: string): void {
    this.tasks.push({ description, completed: false });
} 
```

现在，您可以在浏览器中查看它了。现在您应该能够添加一个新任务，提交表单并看到它被添加到下面的列表中。

## 道具呢？

到目前为止，我已经展示了如何使用子组件、数据属性和事件。但是在任何真实世界的用例中，您通常都必须使用道具。

让我们添加一个新特性，这样我们就可以轻松地定制表单按钮的文本。我们希望能够通过一个叫做`button-text`的道具来传递值。

首先，我们将更新模板，这样我们就可以将一个道具传递给子组件。

```
<to-do-form @added="addTask" button-text="Create Task" /> 
```

我们需要做的下一件事是在我们的子组件中接受道具。为了做到这一点，我们将使用另一个装饰器，`@Prop`。更新我们的`ToDoForm.ts`文件中的 import 语句，以便我们可以使用装饰器。

```
import { Component, Vue, Prop } from 'vue-property-decorator'; 
```

现在我们可以开始使用它了。将装饰器添加到类中以接受道具。您的`ToDoForm.ts`文件现在应该看起来像这样。

```
import { Component, Vue, Prop } from 'vue-property-decorator';
import WithRender from './to-do-form.html';

@WithRender
@Component
export default class ToDoForm extends Vue {

  @Prop(String) readonly buttonText!: string

  public task: string = '';

  public emitTask(): void {
    this.$emit('added', this.task);
    this.task = '';
  }
} 
```

您会注意到，对于 prop decorator，我们声明了两次类型。让我解释一下这是怎么回事。我们第一次指定它时，我们将它作为参数传递给装饰器。这是为了 Vue 的类型检查。这类似于用下面的方式声明属性。

```
buttonText: {
  type: String
} 
```

我们还在属性的末尾指定了类型。这是为了 TypeScript 的类型检查。

我们现在应该能够更新我们的`to-do-form.html`模板来引用属性，而不是硬编码的值。

```
<form @submit.prevent="emitTask">
    <input type="text" placeholder="Enter task..." v-model="task">
    <button type="submit">{{ buttonText }}</button>
</form> 
```

但是，如果我们不经过一个物业呢？您会注意到，我们只会得到一个空按钮。为了安全起见，我们添加一个默认值。为了做到这一点，我们需要向`@Prop`装饰器传递更多的信息。因为我们已经提供了类型，所以我们需要传入一个对象，这样我们就可以配置多个选项。

```
@Prop({ type: String, default: 'Add Task'}) readonly buttonText!: string 
```

这就是事情的全部。如果我们没有提供按钮文本的默认值，现在我们有了一个默认值。

## 结论

这篇文章有点长，但我希望它是有用的。我们已经了解了如何使用子组件、数据属性、事件和道具。

如果你有任何问题，请随时联系我，我会尽力回答。

如果你想看这个的代码，我已经把它推到 git 仓库了。你可以在这里找到它-[https://github.com/georgehanson/vue-todo-typescript](https://github.com/georgehanson/vue-todo-typescript)

## 作业

如果你喜欢，这里有一个小任务给你做。

首先分叉存储库。接下来添加一个特性，以便您可以将任务标记为完成。但是不要使用`v-model`，让每个任务成为它自己的组件并使用事件。

祝你好运！