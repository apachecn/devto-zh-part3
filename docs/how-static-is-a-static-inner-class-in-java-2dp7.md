# Java 中的静态内部类有多静态？

> 原文：<https://dev.to/erikpischel/how-static-is-a-static-inner-class-in-java-2dp7>

回答:一点也不静态。静态内部类的行为就像普通类一样，只是它位于外部类的名称空间中(“为了打包方便”，正如官方 Java 教程[所说的](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html))。

所以举个例子:

```
public class Outer { 
  private int x = 0; 
  static class Inner { 
    //... 
  }
} 
```

与真正的内部(嵌套)类相反，你不需要一个外部的实例来创建一个内部的实例:

```
Outer.Inner inner = new Outer.Inner(); 
```

内部实例没有关于外部实例的特殊知识。内部类的行为就像一个顶级类，它只是必须被限定为“外部”类。内心”。

## 为什么我要写这个？

因为我的两个同事(都是经验丰富的 Java 开发人员)不确定一个静态内部类是否是关于静态成员的，因此也不确定全局状态，这让我非常震惊。

也许他们不使用静态内部类。

## 什么时候使用静态内部类？

我使用一个静态内部类

1.  当它只对外部类有用并且独立于外部类的(私有)成员时，
2.  当它在概念上与外部类(例如构建器类)联系在一起时
3.  为了包装方便。

通常，静态内部类的可见性不是公共的。在这种情况下，我是在同一个源文件中创建一个静态内部类还是一个顶级类没有太大的区别。因此，第一个代码示例的替代代码是:

```
public class Outer { 
  // ...
}
// not really inner any more
class Inner { 
  // ... 
} 
```

(2)的一个例子是构建器类:

```
public class Thing { 
  //... 
  public static class Builder { 
    // ... many withXXX methods 
    public Thing make() // ... 
  }
} 
```

如果内部实例需要访问外部实例的(私有)成员，那么内部实例需要是非静态的。

[![](img/dbe2291da905e3c24c66e26821641a1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--okY2Qu4z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.epischel.de/analytics/piwik.php%3Fidsite%3D3%26rec%3D1%26url%3Dhttps%253A%252F%252Fon-sw-integration.epischel.de%252F2019%252F03%252F02%252Fhow-static-is-a-static-inner-class-in-java%252F%253Fpk_campaign%253Dfeed%2526pk_kwd%253Dhow-static-is-a-static-inner-class-in-java%26action_name%3DHow%2Bstatic%2Bis%2Ba%2Bstatic%2Binner%2Bclass%2Bin%2BJava%253F%26urlref%3Dhttps%253A%252F%252Fon-sw-integration.epischel.de%252Ffeed%252F)