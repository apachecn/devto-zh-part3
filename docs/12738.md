# 数据绑定和 GORM 对象

> 原文：<https://dev.to/joemccall86/databinding-and-gorm-objects-1hka>

在 Groovy 中，您可以这样做:

```
class Widget {
  String name
}

def widget = new Widget(name: '')
assert widget.name == '' 
```

Enter fullscreen mode Exit fullscreen mode

当这个小部件是一个 GORM 对象(在 Grails 应用程序中)时，默认配置不允许这样做:

```
// Inside grails-app/domain
class PendingDeletion {
  String name
  String path
}

// Elsewhere in code
def pendingDeletion = new PendingDeletion(name: 'foo', path: '')
assert pendingDeletion.path == '' // Fails; pendingDeletion.path is null 
```

Enter fullscreen mode Exit fullscreen mode

这是因为默认配置指示 Grails 数据绑定器将空字符串转换为 null <sup id="fnref:1">[1](#fn:1)</sup> 。

我不确定为什么这是默认设置。为了让上面的代码片段在您的 Grails 应用程序中工作，请在 application.yml 中指定以下内容:

```
grails:
  databinding:
    convertEmptyStringsToNull: false 
```

Enter fullscreen mode Exit fullscreen mode

这使得 GORM 映射构造函数的行为与普通的 POGO 映射构造函数相同。

或者，当字段值可以为空时，不要使用 GORM map 构造函数。

1.  [http://docs.grails.org/3.3.9/ref/Constraints/nullable.html](http://docs.grails.org/3.3.9/ref/Constraints/nullable.html)↩