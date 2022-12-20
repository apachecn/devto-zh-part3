# 飞镖吸气剂和设置剂

> 原文：<https://dev.to/newtonmunene_yg/dart-getters-and-setters-1c8f>

## 先决条件

你将需要以下方面的基本知识

1.  镖
2.  面向对象编程

Getters 和 setters 是提供对对象属性的读写访问的特殊方法。您的类的每个实例变量都有一个隐式的 getter，如果需要，还有一个 setter。在 dart 中，您可以通过实现自己的 getters 和 setters 来更进一步。如果你有任何面向对象编程的经验，你会感到如鱼得水。让我们开始吧。

在 OOP 中，一个类充当该类(对象)实例的抽象数据类型(ADT)。在 dart 中，也是如此。一个类的基本语法是:

```
class className {
 fields;
 getters/setters
 constructor
 methods/functions
} 
```

Enter fullscreen mode Exit fullscreen mode

getters 和 setters 也可以放在构造函数之后。现在让我们创建一个类并实例化它。

```
class Vehicle {
  String make;
  String model;
  int manufactureYear;
  int vehicleAge;
  String color;

  int get age {
    return vehicleAge;
  }

  void set age(int currentYear) {
    vehicleAge = currentYear - manufactureYear;
  }

  // We can also eliminate the setter and just use a getter.
  //int get age {
  //  return DateTime.now().year - manufactureYear;
  //}

  Vehicle({this.make,this.model,this.manufactureYear,this.color,});
} 
```

Enter fullscreen mode Exit fullscreen mode

年龄在这里既是获取者又是设定者。让我们看看如何使用它。

```
void main() {
 Vehicle car = 
 Vehicle(make:"Honda",model:"Civic",manufactureYear:2010,color:"red");
  print(car.make); // output - Honda
  print(car.model); // output - Civic
  car.age = 2019;
  print(car.age); // output - 9

} 
```

Enter fullscreen mode Exit fullscreen mode

我最喜欢的使用 getters 的方法之一是从一个对象获取一个`Map`。

```
void main() {
 Vehicle car = Vehicle(make:"Honda",model:"Civic",manufactureYear:2010,color:"red");
  print(car.map); // output - {make: Honda, model: Civic, manufactureYear: 2010, color: red}
}
class Vehicle {
  String make;
  String model;
  int manufactureYear;
  int vehicleAge;
  String color;

  Map<String,dynamic> get map {
    return {
      "make": make,
      "model": model,
      "manufactureYear":manufactureYear,
      "color": color,
    };
  }

  int get age {
    return DateTime.now().year - manufactureYear;
  }

  void set age(int currentYear) {
    vehicleAge = currentYear - manufactureYear;
  }

  Vehicle({this.make,this.model,this.manufactureYear,this.color,});
} 
```

Enter fullscreen mode Exit fullscreen mode

这涵盖了 Dart 中 getter 和 setter 的基本用法。关于类还有很多东西要学。我将在以后的帖子中介绍所有内容。另外，请注意我们的构造函数中命名参数的使用。我还将在以后的文章中介绍这些和其他传递参数的方法