# 还能如何定义 JavaScript 对象的属性

> 原文：<https://dev.to/cristicurteanu/how-else-you-can-define-properties-for-javascript-objects-2cap>

JavaScript 是一项了不起的技术。它非常灵活，允许很酷的运行时对象操作，这不像 Ruby 那样令人头疼(尽管这是我在使用两者后的个人观点)。

## 属性定义的常用方式

默认属性定义有几种方式。第一个是使用对象初始化文字。例如，我们有一个`Car`对象:

```
var Car = {
    brand: 'Toyota',
    model: 'Prius',
    engine: {
        state: 'off'
    },
    turnOn: function() {
        this.engine.state = 'on'
        console.log('Engine is:', this.engine.state)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样对象的属性就可以从外界访问，比如:

```
Car.brand // => "Toyota"
Car.brand = 'Honda'
Car.brand // => "Honda"

Car.engine.state // => "off"
Car.turnOn() // => "Engine is: on"
Car.engine.state // => "on" 
```

Enter fullscreen mode Exit fullscreen mode

并且可以用其他类型的值来改变。

属性定义的另一种方式是在函数构造函数中使用`this`关键字，该关键字将引用当前的`Function`对象:

```
function Car() {
    this.brand = 'Toyota'
    this.model = 'RAV 4'
    this.engine = {
        state: 'off'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

并且它们也可以从外部世界访问:

```
var car = new Car()

car.brand // => "Toyota"
car.brand = 'Honda'
car.brand // => "Honda" 
```

Enter fullscreen mode Exit fullscreen mode

但是 JavaScript 中的属性定义还有一些额外的特性，我们将在下一节中介绍。

## 使用**方法定义属性**

根据[文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty#Syntax)介绍，这是一个`Object`的静态方法，第一个参数是一个对象，第二个参数是新属性的名称，一个带选项的对象。让我们看下一个例子:

```
var Car = {
    brand: 'Toyota'
}

Object.defineProperty(Car, 'brand', {
  writable: false,
})

Car.brand // => "Toyota"
Car.brand = 'BMW'
Car.brand // => "Toyota" 
```

Enter fullscreen mode Exit fullscreen mode

这样,`brand`属性就不会被外界覆盖。这样，就有可能设置对象属性的所有方面，并对对象的属性进行很好的控制。以下是应该考虑的一些其他选项:

*   **<u>可配置</u>** -默认为`false`，如果值为`true`，将允许改变该属性的类型或从当前对象中删除该属性

*   **<u>可枚举</u>**——这将指示该属性是否应该在枚举过程中显示，只有当其值为`true`时才会显示。默认情况下是`false`

*   **<u>可写</u>** - `true`如果该属性应该用赋值运算符`=`来改变。默认为`false`

*   **<u>值</u>** -它可以接受任何有效的特定类型的值，比如数字、对象、函数等。

*   **<u>get</u>** -该属性可以将一个函数作为一个值，它将覆盖通过其访问该属性的对象的属性值提取

*   **<u>set</u>**——这个属性可以把一个函数作为一个单参数的值，它可以覆盖访问这个属性的对象的赋值操作符的逻辑。

让我们考虑一个更复杂的例子。如果需要操作对象内部的其他数据，而不需要调用特定的方法，那么定制对象属性的`set`属性是明智的，如下所示:

```
function CustomGettersSetters() {
    var a = null;
    var history = [];

    Object.defineProperty(this, 'a', {
        get() {
            console.log('Inside getter')
            return a
        },
        set(arg) {
            console.log('Inside setter')
            history.push(arg)
            a = arg
            return true
        }
    })

    this.getHistory = function() { return history }
}

var custom = new CustomGettersSetters();

custom.a // will log "Inside getter" and return `null`
custom.a = '321' // will log "Inside setter", push a value to `history`, and will set value to `a`
custom.a = 764 // will log "Inside setter", push a value to `history`, and will set value to `a`
custom.a // will log "Inside getter" and return `764`
custom.history // will return `undefined`
custom.getHistory() // will return an array ["321", 764] 
```

Enter fullscreen mode Exit fullscreen mode

这样就为当前对象添加了一个额外的功能，而不需要任何额外的方法调用。

## 结论

方法对于对象属性操作来说是一个非常强大的工具，因为它允许对它进行某种控制，这在某些时候是很有用的。还有`Object.defineProperties`可以用类似的方式创建多个属性。不同之处在于它有两个参数:第一个是将有新属性的对象，第二个是属性对象，它也将包含上面提到的选项对象。