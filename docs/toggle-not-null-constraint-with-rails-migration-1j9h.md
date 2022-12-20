# 使用 Rails 迁移切换 NOT NULL 约束

> 原文：<https://dev.to/viralpatelblog/toggle-not-null-constraint-with-rails-migration-1j9h>

利用`change_column_null`来添加或删除列上的 **NOT NULL** 约束。`null`标志表示该值是否可以为**空值**。

添加约束(表示列不能为空):

```
change_column_null :users, :name, false 
```

Enter fullscreen mode Exit fullscreen mode

移除约束(允许列为空):

```
change_column_null :users, :name, true 
```

Enter fullscreen mode Exit fullscreen mode

该方法接受可选的第四个参数，用其他值替换现有的+NULL+s。如果需要，在启用约束时使用该行，否则这些行将无效。

> 注意:请注意第四个参数没有设置列的默认值。