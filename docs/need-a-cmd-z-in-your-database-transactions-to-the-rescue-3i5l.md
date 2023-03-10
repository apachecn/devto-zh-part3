# 您的数据库中需要 cmd + Z 吗？拯救交易！

> 原文：<https://dev.to/bertheyman/need-a-cmd-z-in-your-database-transactions-to-the-rescue-3i5l>

假设你要开一家新的巧克力店。你有点像威利·旺卡，所以每周你都会把新发明的产品上传到你的数据库中:

*   创建新的产品类别
*   插入新产品并链接到类别
*   添加相关价格

如果创建新的产品类别失败，产品可能会错过一个类别。反之亦然，一个不存在的产品的价格对世界没有什么用处。决心每周用新思想丰富世界，您开始寻找解决方案:数据库事务。

> **简而言之:你的进口将是全有或全无**。
> 
> 如果有任何失败，数据库中没有任何改变。只有一切正常，你的数据才会被更新。

这个特性经常被忽略，但在 Laravel:
中并不太难

```
DB::transaction(function () {
    // An exception will rollback evertything in the database transaction
    DB::table('product_categories')->insert($productCategories);
    DB::table('products')->insert($products);
    DB::table('prices')->insert($prices);
}); 
```

就这样-你完了！

延伸阅读？

*   [关于数据库事务的相关文档](https://laravel.com/docs/5.8/database#database-transactions)
*   [在 MySql 中直接使用它的(相当老的)教程](https://www.thecreativedev.com/mysql-transaction-tutorial-for-beginners/)

你想分享什么数据库技巧吗？欢迎在评论中讨论！