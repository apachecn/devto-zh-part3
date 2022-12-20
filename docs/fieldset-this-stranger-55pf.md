# 菲尔德集，这个陌生人。

> 原文：<https://dev.to/equinusocio/fieldset-this-stranger-55pf>

田..瓦特？？是的，它仍然存在，但似乎没有人记得它。这个家伙是一个[生活标准表单元素](https://html.spec.whatwg.org/multipage/form-elements.html#the-fieldset-element),用来表示一个表单中的一组元素或者分组相关的元素，比如标签、输入、计量器、段落等等...它并没有在这里结束，它可以嵌套，可以包含任何类型的元素。

你还有兴趣吗？我再告诉你两件事，它也可以接受三个非常有用的 html 属性，帮助你制作漂亮的表单....它不是用来包装单选按钮的！😱

## 规范

> fieldset 元素表示一组组合在一起的表单控件(*或其他内容*),可选地带有标题。标题由第一个 [legend](https://html.spec.whatwg.org/multipage/form-elements.html#the-legend-element) 元素给出，它是 fieldset 元素的子元素，*，如果有的话*。
> 
> — [WHATWG](https://html.spec.whatwg.org/multipage/form-elements.html#the-fieldset-element)

对于这个元素应该做什么以及什么时候使用它是非常清楚的。如上所述，它还可以接受三个属性:`form`、`name`和`disabled`。

**表格**

该属性允许您将一组输入与特定的表单元素相关联，该表单元素不是它的祖先。这意味着您可以解决缺少嵌套表单可能性的问题。

**名称**

要在[表单中使用的组的名称。元素](https://html.spec.whatwg.org/multipage/forms.html#dom-form-elements) API

**禁用**

这与您在按钮和输入上使用的全局属性相同。它立刻禁用所有子元素。

## 一个用例

让我们举一个真实的例子，制作一个带有由复选框控制的禁用部分的表单。在这个表单中，我们将使用标签、输入和输出元素，我们可以通过充分利用字段集属性和一点 javascript 来构建它。

```
<form id="calculatorForm">
  <fieldset>
    <input type="checkbox" id="enableCalc">
    <label for="enableCalc">Enable calc</label>
  </fieldset>

  <fieldset name="calculator" disabled>

    <!-- Default formula -->
    <fieldset name="defaultcalc">
      <input type="checkbox" id="default" checked>
      <label for="default">Use default calculation</label>
    </fieldset>

    <!-- Custom formula -->
    <fieldset name="customcalc" disabled>
      <label>Use custom calculation</label>
      <input type="number" value="50" id="c">+
      <input type="number" value="50" id="d">=
      <output id="x" for="c d">100</output>
    </fieldset>

  </fieldset>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们有一个带有名为“calculator”的字段集的表单，默认情况下该字段集是禁用的。点击`enableCalc`复选框时，计算器区域将被启用。这是现场演示。

[https://jsfiddle.net/equinusocio/2fm4Lksc/embedded/result,html//dark](https://jsfiddle.net/equinusocio/2fm4Lksc/embedded/result,html//dark)

除了 javascript 集成，这里的重点是`fieldset`元素。它是实时的、标准的、有用的。

## “坏”的例子从...

下面的例子既不错误也不正确，但是为什么不打破这个“不太好的实践”的循环，开始像我们改进 javascript 代码一样改进我们的代码呢？

### 推特自举

```
<div class="form-group">
  <label for="formGroupExampleInput">Example label</label>
  <input type="text" class="form-control" id="formGroupExampleInput">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### ZURB 基金会

```
<div class="input-group">
  <input class="input-group-field" type="url">
  <div class="input-group-button">
    <input type="submit" class="button" value="Submit">
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 材料设计组件

```
<div class="mdc-form-field">
  <div class="mdc-checkbox">
    <input type="checkbox" id="my-checkbox" class="mdc-checkbox__native-control"/>
    <div class="mdc-checkbox__background">
      ...
    </div>
  </div>
  <label for="my-checkbox">This is my checkbox</label>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 顺风 CSS

```
<div class="mb-4">
  <label class="block text-grey-darker" for="username">Username</label>
  <input id="username" type="text" placeholder="Username">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，许多流行的框架不使用字段集(bootstrap 提到它，但仅此而已)作为表单元素容器，这真的很奇怪，因为 HTML 拥有我们构建网页和应用程序所需的所有工具。我们不应该重新创建平台已经提供的东西，相反，我们只需要学习它，并最终如何扩展它，例如使用 [web 组件](https://equinsuocha.io/blog/web-components-the-right-way)。