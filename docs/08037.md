# 构造函数

> 原文：<https://dev.to/vish448/constructor-function-1cd9>

构造函数是创建新对象的函数。它们定义了属于新对象的属性和行为。将它们视为创建新对象的蓝图。

```
 function Car(){
    this.brand = "BMW",
    this.model = "3 series",
    this.year = 2019
} 

```

此构造函数定义了一个汽车对象，其属性名称、型号和年份分别设置为 BMW、3 Series 和 2019。

### 构造函数遵循一些约定:

*   构造函数用大写的名称定义，以区别于其他不是构造函数的函数。
*   构造函数使用关键字 this 来设置它们将创建的对象的属性。在构造函数内部，这是指它将创建的新对象。
*   构造函数定义属性和行为，而不是像其他函数那样返回值。

### 使用构造函数创建对象

`let whiteCar = new Car()`
注意，调用构造函数时使用了 new 运算符。
这告诉 JavaScript 创建一个名为 whiteCar 的 Car 新实例。如果没有 new 运算符，构造函数中的这个就不会指向新创建的对象，从而产生意外的结果。

现在，whiteCar 拥有了汽车构造函数中定义的所有属性:

```
 whiteCar.brand; // BMW
    whiteCar.model; // 3 series
    whiteCar.year; // 2019 
```

Just like any other object, its properties can be accessed and modified:

```
 whiteCar.brand = 'Nissan';
whiteCar.brand; // => Nissan 

```

### 接收参数的构造函数

从上面的例子我们可以看到，我们可以通过创建一个新的实例来访问和修改一个对象的值，并对它应用特定的值。如果它有很多属性，那将是一项很大的工作。因此，为了避免这种情况，我们可以向构造函数发送一个值作为参数。

```
 function Car(brand, model, year) {
  this.brand = brand;
  this.model = model;
  this.year = year;
} 
```

Then pass in the values as arguments to define each unique car into the Car constructor: `let bmwCar = new car("BMW", "3 Series", 2019);` so now bmwCar has following properties

> bmwCar.brand //"宝马"
> bmwCar.model // "3 系"
> bmwCar.year //2019

构造函数更加灵活。现在可以在创建时为每辆汽车定义属性，这是 JavaScript 构造函数如此有用的一个原因。它们根据共享的特征和行为将对象组合在一起，并定义一个自动创建它们的蓝图。

### 用 instanceof

验证对象的构造函数

每当一个构造函数创建一个新的对象时，这个对象就被称为它的构造函数的一个实例。JavaScript 用 instanceof 操作符提供了一种验证这一点的便捷方法。
instanceof 允许您将一个对象与一个构造函数进行比较，根据该对象是否是用构造函数创建的来返回 true 或 false。

```
 let Car(brand, model, year) {
  this.brand = brand;
  this.model = model;
  this.year = year;
}

let newcar = new Car("Honda", "2011");

newcar instanceof Car; // true 
```

If an object is created without using a constructor, instanceof will verify that it is not an instance of that constructor:

```
 let newtoyota = {
    brand : "Toyota",
    model : "camery",
    year: 2010
}

newtoyota instanceof car; // false 

```

### 结论

这是构造函数的基本用法。它将帮助你入门，并给出构造函数如何工作的基本理解。