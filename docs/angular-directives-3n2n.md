# 角度方向

> 原文：<https://dev.to/gugadev/angular-directives-3n2n>

策略是更改组件外观或行为的属性。想象一下它就像一个 CSS/JS 代码“注入器”。指令是一个装饰用“`@Directive`”装饰的类，装饰者接受对象。在对象的属性中，我们传递了一个称为“`selector`”的 escencial，通过它，我们向您提供了要注入到其他组件中的属性的名称。让我们看看它看起来怎么样:

```
import { Directive } from '@angular/core'

@Directive({
  selector: '[myAwesomeAttribute]'
})
export class MyAwesomeDirective {

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想使用这个属性，那就在构件的模板中:

```
<div myAwesomeAttribute></div> 
```

Enter fullscreen mode Exit fullscreen mode

但是，指令本身并不执行任何操作。要修改图元的外观或行为，必须使用某些工具-什么

### 改变外貌

若要将 CSS 类注入元素中，可以通过两种方式执行此操作:

*   乌桑多`ElementRef`
*   乌桑多`@HostBinding`

**ElementRef**

允许我们访问策略的本地(主机)元素。例如，如果主机是`div`元素，我们将从 DOM 中获取该元素。这样做的方式如下:

```
constructor(ref: ElementRef) {
  const el = ref.nativeElement as HTMLElement
  el.classList.add('my-awesome-class')
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码告诉角儿:“嘿，我要你把我注入构造函数中，引用这个指令口渴之处的元素。”。完成此操作后，我们可以通过属性`nativeElement`访问 DOM 元素。从这里开始，要看你想对他做什么了。

**主机绑定**

通过这种装饰，我们可以“绑定”主机上的数据。例如，我们可以告诉你要注入哪些 css 类而无需使用 feo `ElementRef`。这是通过将目标元素的属性/属性名称作为装饰属性来完成的。让我们看一下上面的示例如何使用此方法。

```
// Lo hacemos directamente como propiedad
@HostBinding('class') elementClass = 'my-awesome-class' 
```

Enter fullscreen mode Exit fullscreen mode

而这足以把‘t0’课注入元素中。

### 改变行为

我们看到了`@HostBinding`如何让我们改变外貌。还有另一位名为`@HostListener`的装饰家，使我们得以**倾听**元素的事件，并扩展其功能。让我们看看一个例子:

```
import { Directive, HostListener } from '@angular/core'

@Directive({
  selector: '[myAwesomeAttribute]'
})
export class MyAwesomeDirective {
  @HostListener('click')
  onElementClick() {
    // hacer algo
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们成功地扩展了目标元素的功能。简单吗？

## 设置值

策略还允许我们从主机设置值。这是通过与组件相同的方式实现的:通过装饰器`@Input`。

```
// como propiedad
@Input() property: string 
```

Enter fullscreen mode Exit fullscreen mode

我们会这样使用它:

```
<div myAwesomeDirective="hi"></div> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 例

让我们举一个例子，包括我们所看到的指令的三个特征:`@HostListener`、`@HostListener`和`@Input`。

```
@Directive({
  selector: '[appMemoButton]'
})
export class MemoButtonDirective {
  @Input('appMemoButton') key: string
  @HostBinding('class') elementClass = 'btn btn--memo'

  @HostListener('click')
  public onClick() {
    const counter = parseInt(localStorage.getItem(this.key), 10)
    localStorage.setItem(this.key, counter + 1)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<button appMemoButton="registerButtonClicks">Register</button> 
```

Enter fullscreen mode Exit fullscreen mode

以上例子相当简单。一般的想法是在浏览器中保存单击按钮的次数。

为此，我们假定每个按钮都将自己的 id 传递到策略中以进行保存，并且不会与其他按钮混淆。

```
@Input('appMemoButton') key: string 
```

Enter fullscreen mode Exit fullscreen mode

下面是添加我们以前定义的 CSS 类。

```
@HostBinding('class') elementClass = 'btn btn--memo' 
```

Enter fullscreen mode Exit fullscreen mode

最后，当您按下按钮时，我们会截取事件，以新增一个单位到按钮的点击计数中。

```
@HostListener('click')
public onClick() {
  const counter = parseInt(localStorage.getItem(this.key), 10)
  localStorage.setItem(this.key, counter + 1)
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 结论

准则可能非常有用；用途多种多样。我们可以使用它们来添加主题、扩展事件等。例如，UI 套件是一个非常常见的用法，它通过策略定义按钮类型、文本框、下拉列表等。你给他的用法一如既往地取决于你。😉