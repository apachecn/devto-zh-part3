# 如何在 Swift 中重载函数

> 原文：<https://dev.to/onmyway133/how-to-overload-functions-in-swift-52ch>

## 功能

Swift 中的功能可通过以下方式区分

*   参数标签
*   参数类型
*   返回类型

所以这些都是有效的，对`subscript`和
都有效

```
struct A {

  // return type
  func get() -> String { return "" }
  func get() -> Int { return 1 }

  // mix of parameter type and return type
  func get(param: String) -> String { return "" }
  func get(param: String) -> Int { return 1 }
  func get(param: Int) -> Int { return 1 }
  func get(param: Int) -> String { return "" }

  subscript(param: String) -> String { return "" }
  subscript(param: String) -> Int { return 1 }
  subscript(param: Int) -> Int { return 1 }
  subscript(param: Int) -> String { return "" }

  // parameter label
  func set(int: Int) {}
  func set(string: String) {}

  // concrete type from generic
  func get(param: Array<String>) -> String { return "" }
  func get(param: Array<Int>) -> Int { return 1 }

  subscript(param: Array<String>) -> String { return "" }
  subscript(param: Array<Int>) -> Int { return 1 }
} 
```

当你专门化一个泛型类型时，比如`Array<Int>`，你实际上是在使用一个具体类型

不幸的是，这不适用于 NSObject 子类

> 具有目标 C 选择器“get”的方法“get()”与具有相同目标 C 选择器
> 的先前声明冲突

```
class B: NSObject {

  func get() -> String { return "" }
  func get() -> Int { return 1 }
} 
```

## 通用功能

我们也可以控制通用函数

```
func f<T>(t: T) {
  print("T")
}

func f(string: String) {
  print("String")
}

func f(int: Int) {
  print("Int")
} 
```

原帖[https://github.com/onmyway133/blog/issues/211](https://github.com/onmyway133/blog/issues/211)