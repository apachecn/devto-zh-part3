# 从 tablayout 中移除触摸效果

> 原文：<https://dev.to/callas1900/remove-touch-effect-from-tablayout-mj3>

使用 Android 的 Tablyout 会有波纹和触摸效果。您可以通过下面的方法来消除这种影响。

标签下面的内部

```
<android.support.design.widget.TabLayout 
```

您应该在下面添加属性

```
app:tabRippleColor="@null" 
```

就这样。