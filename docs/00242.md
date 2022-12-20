# Angular 8 数据绑定教程:构建您的第一个 Angular 应用程序

> 原文：<https://dev.to/techiediaries/angular-8-data-binding-tutorial-build-your-first-angular-app-53lp>

在本教程中，我们将继续使用 Angular 8 构建我们的计算器应用程序。我们将特别学习数据绑定，包括事件绑定和属性绑定。

到目前为止，我们的模板只是普通的 HTML，但是 Angular 提供了我们可以在模板中使用的其他结构，比如数据绑定(插值、事件和属性绑定)。

简而言之，数据绑定是 Angular 中的一个基本概念，它允许开发人员在组件和它的视图(更准确地说是 DOM)之间进行通信。这样，您就不需要手动地将数据从组件推送到 DOM，然后再推回来。

Angular 提供了四种类型的数据绑定，它们在数据流动的方式上有本质的不同，即从组件到 DOM，从 DOM 到组件，或者两种方式都有:

*   插值:数据从组件流向 DOM——它用于显示相关模板中组件成员变量的值。我们用花括号进行插值。

*   属性绑定:数据从组件流向 DOM 中元素的属性。它用于将组件成员变量绑定到 DOM 的一个属性，比如一个`<input>`标记的 value 属性(例如:`<input type="text" [value]="foobar">)`)。我们使用括号进行属性绑定。

*   事件绑定:数据从 DOM 流向组件。当一个 DOM 事件(比如单击)被触发时，组件的绑定方法被调用。例如:`<button (click)="sayHi()">Hi</button>`-`sayHi()`方法将被调用，因此需要在组件类中定义它。我们使用括号进行事件绑定。

*   双向数据绑定:数据双向流动。比如:`<input type="text" [(ngModel)]="foobar">`(foobar 变量需要在组件中定义)。input 元素和 foobar 将具有相同的值，当其中一个发生变化时，另一个也会相应地变为相同的值。我们使用盒子中的香蕉语法，它结合了括号和圆括号，用于双向数据绑定。ngModel 是一个特殊的指令，它绑定到`<input>`和`<textarea>`元素的值属性，但是您可以为 DOM 或组件中的任何属性构造双向数据绑定。双向数据绑定=属性绑定+事件绑定。

有了这些信息，让我们实现我们的计算器应用程序。

我们有 DOM 元素用于操作和数字，以及在哪里显示操作的结果。

当用户点击相应的 DOM 元素时，我们需要获得数字的值或操作的类型，计算结果并显示在结果元素中。

在模板中，我们有四组密钥:

*   数字(0-9)，
*   运算符(+，-，*，/，=)，
*   小数点(。)和一个重置键。

让我们看看如何使用 Angular 来监听计算器上的滴答声，并确定按下了哪种类型的键。

打开`src/app/calculator/calculator.component.ts`文件，开始定义组件的以下成员变量:

```
export class CalculatorComponent implements OnInit {

  currentNumber = '0';
  firstOperand = null;
  operator = null;
  waitForSecondNumber = false; 
```

Enter fullscreen mode Exit fullscreen mode

*   `currentNumber`变量保存将在结果输入元素中显示的字符串。

*   `firstOperand`变量保存操作的第一个操作数的值。

*   `operator`变量保存操作。

*   `waitForSecondNumber`变量保存一个布尔值，指示用户是否已经完成输入第一个操作数，并准备好输入操作的第二个操作数。

接下来，定义用于获取当前数字的`getNumber()`方法:

```
 public getNumber(v: string){
    console.log(v);
    if(this.waitForSecondNumber)
    {
      this.currentNumber = v;
      this.waitForSecondNumber = false;
    }else{
      this.currentNumber === '0'? this.currentNumber = v: this.currentNumber += v;

    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，定义将小数点附加到当前数字的`getDecimal()`方法:

```
 getDecimal(){
    if(!this.currentNumber.includes('.')){
        this.currentNumber += '.'; 
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，定义根据运算符类型执行计算的`doCalculation()`方法:

```
 private doCalculation(op , secondOp){
    switch (op){
      case '+':
      return this.firstOperand += secondOp; 
      case '-': 
      return this.firstOperand -= secondOp; 
      case '*': 
      return this.firstOperand *= secondOp; 
      case '/': 
      return this.firstOperand /= secondOp; 
      case '=':
      return secondOp;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，定义将用于获取所执行操作的`getOperation()`:

```
 public getOperation(op: string){
    console.log(op);

    if(this.firstOperand === null){
      this.firstOperand = Number(this.currentNumber);

    }else if(this.operator){
      const result = this.doCalculation(this.operator , Number(this.currentNumber))
      this.currentNumber = String(result);
      this.firstOperand = result;
    }
    this.operator = op;
    this.waitForSecondNumber = true;

    console.log(this.firstOperand);

  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，定义将用于清除结果区域和重置计算的`clear()`方法:

```
 public clear(){
    this.currentNumber = '0';
    this.firstOperand = null;
    this.operator = null;
    this.waitForSecondNumber = false;
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您需要使用数据绑定将这些方法绑定到模板。

## 角度属性绑定示例

我们已经在组件中定义了变量和方法。现在，我们需要将它们绑定到模板上。

让我们从保存当前输入数字的值的`currentNumber`变量开始。让我们使用属性绑定将`currentNumber`绑定到`<input>`元素的值属性，如下所示:

```
<div class="calculator">

  <input type="text" class="calculator-screen" [value]="currentNumber" disabled />

  <!-- [...] --> 
```

Enter fullscreen mode Exit fullscreen mode

我们用括号将 value 属性括起来，创建一个属性绑定。

现在，我们的当前数字将显示在我们的计算器中，当`currentNumber`变量的值改变时，显示的值将相应地改变，而不必手动添加任何代码来更新 DOM。

## 角事件通过例子绑定

接下来，当点击一个数字按钮时，我们需要调用`getNumber()`方法将数字附加到当前的数字字符串中。为此，我们可以使用角度事件绑定将 getNumber()方法绑定到显示数字的按钮的 click 事件。按如下方式更改您的组件模板:

```
<div class="calculator">

  <input type="text" class="calculator-screen" [value]="currentNumber" disabled />

  <div class="calculator-keys">

    <!-- [...] -->

    <button type="button" (click) = "getNumber('7')" value="7">7</button>
    <button type="button" (click) = "getNumber('8')" value="8">8</button>
    <button type="button" (click) = "getNumber('9')" value="9">9</button>

    <button type="button" (click) = "getNumber('4')" value="4">4</button>
    <button type="button" (click) = "getNumber('5')" value="5">5</button>
    <button type="button" (click) = "getNumber('6')" value="6">6</button>

    <button type="button" (click) = "getNumber('1')" value="1">1</button>
    <button type="button" (click) = "getNumber('2')" value="2">2</button>
    <button type="button" (click) = "getNumber('3')" value="3">3</button>

    <button type="button" (click) = "getNumber('0')" value="0">0</button>
    <!-- [...] -->
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 click 事件周围的括号来创建一个事件绑定。

接下来，让我们将`getOperation()`、`getDecimal()`和`clear()`方法绑定到它们各自按钮的点击事件:

```
 <div class="calculator-keys">

    <button type="button" (click) = "getOperation('+')" class="operator" value="+">+</button>
    <button type="button" (click) = "getOperation('-')" class="operator" value="-">-</button>
    <button type="button" (click) = "getOperation('*')" class="operator" value="*">&times;</button>
    <button type="button" (click) = "getOperation('/')" class="operator" value="/">&divide;</button>

    <!-- [...] -->

    <button type="button" (click) = "getDecimal()" class="decimal" value=".">.</button>
    <button type="button" (click) = "clear()"  class="all-clear" value="all-clear">AC</button>

    <button type="button" (click) = "getOperation('=')" class="equal-sign" value="=">=</button>

  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们的计算器准备好了！

## 结论

在本教程中，我们已经用 Angular 8 完成了简单的计算器应用程序的构建。我们已经了解了数据绑定的类型，并且看到了 Angular 8 中事件和属性绑定的一个例子。