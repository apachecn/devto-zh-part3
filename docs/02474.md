# 主题

> 原文：<https://dev.to/rakchamp/themes-1o90>

大家好，让我们一起来看看我的主题之旅。每当我们创建一个新的主题，我们必须处理 CSS。因此，我们再次用不同的颜色编写相同的 CSS，这使得代码重复，就像下面的脚本。

**灯光主题**

```
product .light-theme {
   background-color: white;
   color: blue;
}
product .light-theme:hover {
   background-color: blue;
   color: white;
} 
```

**黑暗主题**

```
product .dark-theme {
   background-color: black;
   color: white;
}
product .dark-theme:hover {
   background-color: white;
   color: black;
} 
```

为了优化，自定义属性在这个 CSS 中起着重要的作用。有趣的..？

**那么什么是自定义属性呢？**

自定义属性是一个预定义的变量，它存储 CSS 属性的值。

好吧，那如何优化呢？

每当我们更新变量 *`within the scope`* (自定义属性)时，它会自动更新使用相同变量的属性(自定义属性)的值。

[https://codepen.io/ajay25kools/embed/BeRMRm?height=600&default-tab=result&embed-version=2](https://codepen.io/ajay25kools/embed/BeRMRm?height=600&default-tab=result&embed-version=2)

很酷吗？

让我们看看代码，

```
:root{
  --libg : white;
  --licolor : black;
  --borderleft : blue;
  --libghover : #e1e1e8;
  --lihovercolor : blue;
}
.sidebar {
  width: 220px;
  .light {
    li {
      background-color : var(--libg);
      color : var(--licolor);
      border-left : 5px solid var(--borderleft);
      &:hover {
        background-color : var(--libghover);
        color : var(--lihovercolor);
      }
    }
  }
} 
```

自定义变量
制作的黑暗主题

```
.sidebar {
  .dark {
  --libg : #151f2a;
  --licolor : white;
  --borderleft : blue;
  --libghover : black;
  --lihovercolor : white;
  }
} 
```

**最终想法**

1.  冗余 CSS 减少。
2.  易于定制。
3.  不需要跟踪 CSS 属性。

希望你搞清楚这个概念。让我们在另一段旅程中再见。感谢阅读。