# 分子

> 原文：<https://dev.to/leonorader/molecules-2g6h>

所以最近我开始了一个项目。它是一个有用的 Java 类库，带有内置的验证。

## 不好意思，什么？

想一个人。现在试着让一个人成为一个阶级。我猜你会这样做:

```
public class Person {
    private String firstName;
    private String lastName;
    private Date birthDate;
    private String email;    
} 
```

Enter fullscreen mode Exit fullscreen mode

你能看到我们有这些属性:姓名和电子邮件地址，但是我们必须将它们映射成字符串、日期等类型。不要忘记在服务或控制器层验证这些数据...

那么，除了这些，为什么我们不能有名字或电子邮件类型？这些类型应该知道什么是有效的电子邮件地址，或者名称的最大长度是多少。

通过使用分子，你的 Person 类可以看起来像这样:

```
public class Person {
    private Name firstName;
    private Name lastName;
    private BirthDate birthDate;
    private Email email;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 明白了。现在怎么办？

你喜欢这个主意吗？检查所有的[分子](https://github.com/floppylab/molecules#what-molecules-are-available)或者求助！:)

目前我正处于早期阶段，所以任何帮助和/或建议都是非常感谢的！:)

查看这里:[https://github.com/floppylab/molecules](https://github.com/floppylab/molecules)