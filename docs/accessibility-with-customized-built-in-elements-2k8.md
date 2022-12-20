# 使用定制的内置元素使 web 更容易访问

> 原文：<https://dev.to/steveblue/accessibility-with-customized-built-in-elements-2k8>

为了使我们的网络应用程序可访问，我们有 WAI-ARIA 供我们使用。WAI-ARIA 规范包括一系列可以添加到 DOM 元素中的属性，这些属性为屏幕阅读器等工具提供了额外的上下文，帮助盲人阅读网站内容。

```
<div role="button"></div> 
```

Enter fullscreen mode Exit fullscreen mode

通过向这个 div 添加角色属性，我们让屏幕阅读器将这个 div 解释为另一个按钮。这是朝着正确方向迈出的一步，但是我们没有获得 button 元素的所有特性，这些特性使它比 div 更容易访问。

## 按钮

`HTMLButtonElement`默认允许用户通过键盘导航。当用户按下键盘上的 tab 键时，按钮将获得焦点。

如果您监听按钮上的点击事件，当用户按下`Enter`键时，这个事件也将被触发。这种功能被嵌入到按钮中，使那些不能用鼠标而是依靠键盘来浏览网站的用户更容易使用它。

```
button.addEventListener('click', onButtonClick) 
```

Enter fullscreen mode Exit fullscreen mode

在 div 上使用按钮的唯一缺点是需要一些额外的样式来覆盖按钮元素的默认外观。与我们为不能用键盘使用 div 的最终用户提供的拦截器相比，这是一个小小的开发障碍。

## 定制内置元素

如果我们想给按钮添加更多的功能，但保留 HTMLButtonElement 的所有可访问性，该怎么办？

定制的内置元素来拯救！

在这个例子中，我们使用 fetch API 发出一个请求，并根据请求是成功还是出错来设置按钮的样式。这演示了如何使用带有定制元素 v1 API 的`connectedCallback`生命周期挂钩来添加 click 的事件侦听器，然后发出请求，并基于请求的结果调用在该类上定义的定制方法(`onSuccess`和`onError`)之一。

```
class MyButton extends HTMLButtonElement {
  constructor() {
    super();
  }
  connectedCallback() {
    this.addEventListener('click', this.onClick);
  }
  onClick() {
    fetch('http://example.com/some.json')
    .then(response => this.onSuccess)
    .catch(error => this.onError);
  }
  onSuccess() {
    this.classList.add('is--success');
  }
  onError() {
    this.classList.add('is--error');
  }
}
customElements.define('my-button', MyButton, { extends: 'button' }) 
```

Enter fullscreen mode Exit fullscreen mode

此示例的最后一行允许浏览器将元素解释为 HTMLButtonElement 的扩展。这里与自主定制元素的主要区别是第三个参数，我们传入一个带有`extends`属性的对象。

为了在模板中使用新的定制内置元素，我们像使用任何其他按钮一样使用它，但是使用了一个新的`is`属性。这个属性告诉浏览器在文档被解析后创建一个`MyButton`类的实例。

```
<button is="my-button"></button> 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。现在我们有了一个可以通过键盘访问的自定义按钮元素。本质上，这里发生的事情是浏览器把我们的类当作一个 mixin，把它的功能和默认按钮元素的功能结合起来。

### 兼容 JavaScript 框架

在各种 JavaScript 框架中，对定制内置元素的支持是不稳定的。例如，Angular 不处理“is”属性的这种特殊用例，也不像您预期的那样编译定制的内置元素。这是一个棘手的用例，因为浏览器在呈现文档时解释“is”属性，而不是在 JavaScript 框架引导或向文档添加 DOM 之后。IMHO JavaScript 框架也应该解释“is”属性，因为定制的内置元素提高了可访问性，而这在开发过程中有时会丢失。

### 动态定制内置元素

为了克服这个限制(如果它存在于您选择的框架中)，您可以动态地创建一个定制的内置元素，并使用`document.createElement`将其添加到您的模板中。这个方法接受第二个参数，让浏览器将这个新元素解释为我们的`MyButton`类的一个实例。

```
const myButtonInstance = document.createElement('button', { is: 'my-button' });
this.template.appendChild(myButtonInstance); 
```

Enter fullscreen mode Exit fullscreen mode

如果框架需要绑定到定制元素的属性或内容，这种方法会有一些限制，但是这种方法可以动态地呈现定制的内置元素。

### 浏览器兼容性

截至 2019 年 3 月，evergreen 浏览器部分支持自定义元素 v1 规范，更喜欢自主自定义元素，而不是自定义内置元素。只有 Chrome 和 Firefox 支持开箱即用的定制内置元素。微软已经在 Edge 中确定了支持范围，但是 WebKit 发誓永远不支持这个规范。这真的很遗憾。这位工程师真的不明白为什么苹果会阻止一个对实现网络可访问性如此有帮助的 API。对于不支持定制内置元素的浏览器，需要这个 [polyfill](https://github.com/ungap/custom-elements-builtin) 。

### 结论

通过使我们的网络应用程序更容易访问，我们向那些不能用鼠标或触摸设备导航的人开放了互联网。想象一下，如果你只能使用键盘来导航一个网络应用程序，或者只能通过语音命令来导航。如果你不能有效地使用网络应用，那将是一个令人沮丧的混乱局面。定制的内置元素允许您将功能与已经提供辅助功能的元素混合在一起。在开发 web 应用程序的过程中使用定制的内置元素，使互联网成为一个更容易访问的地方。