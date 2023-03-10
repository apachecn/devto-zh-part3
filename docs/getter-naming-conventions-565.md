# Getter 命名约定

> 原文：<https://dev.to/bertilmuth/getter-naming-conventions-565>

在没有内置属性概念的 Java 和类似语言中，通常只通过 getter 方法将字段设为私有，以便对象外部可以访问。
Java Beans 命名约定建议对字段`x`的 getter 方法使用名称`getX()`(大多数情况下)，比如:

```
public class User{
    private Name name;

    public Name getName(){
        return name;
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

刚开始编程的时候没质疑过。后来我听说有几个人，比如马丁·福勒，根本不在乎那个大会。他们为示例 getter 编写了`name()`。

看起来好多了，就开始领养了。然后我遇到了期望 Beans 约定的库的问题。
遵循 Beans 约定的类的另一个好处是:在 IDE 中，如果您只需键入前三个字母:`get`，就可以很容易地找到库的类的所有属性。

那么，如果您使用 getter 方法，您使用哪种命名约定呢？