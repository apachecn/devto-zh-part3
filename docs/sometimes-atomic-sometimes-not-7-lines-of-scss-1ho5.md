# 有时是原子的，有时不是。SCSS 的 7 行

> 原文：<https://dev.to/therealkevinard/sometimes-atomic-sometimes-not-7-lines-of-scss-1ho5>

我喜欢原子 css 的灵活性和一致性的平衡，但我不一定喜欢它的前端有多冗长。

我合作的设计师也不知道。

尽管如此，互联网也不喜欢它。

* * *

七行 SCSS 代码——不包括配置变量——可以减轻负担:

```
// pre-compose config 
$composables: (
        'brand-button-orange': (
                '.btn', '.btn-brand-orange-300', '.font-weight-bold', '.text-white',
        ),
        'brand-header-base': (
                '.text-white', '.ml-md-r4',
        ),
        'brand-header-subline': (
                '.brand-header-base', '.font-size-xlg',
        ),
);

// pre-compose helper 
@each $class, $class_spec in $composables {
  .#{$class} {
    @each $extend in $class_spec {
      @extend #{$extend};
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这只是一点点递归字符串插值，但这已经解决了我的团队中的一大堆混乱。如果您还不知道发生了什么:

1.  `$composables`配置变量是一个映射，它引用一个命名键下的一组原子规则。
2.  助手通过`@extend`它映射到的每个规则创建一组新的类

添加/更改组合非常简单。反过来，现在设计可以用

`<a href="#" class="brand-button-orange"></a>`

代替

`<a href="#" class="btn btn-brand-orange-300 font-weight-bold text-white"></a>`。

当然，由于我们只是使用了`@extend`，这实际上只是一种简化/方便的包装，所以:

*   我们制造的板材相当精简/高效
*   我们保持与底层原子的一致性
*   我们不会牺牲前端在组合类上添加更多实用程序的能力。