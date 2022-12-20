# 记录数据库:数据字典

> 原文：<https://dev.to/andercodes/documenting-the-db-data-dictionaries-17n>

你好 DEV 社区！这是我大学的项目月。我已经在两个项目中工作了几个星期，压力很大，但我学到了很多。我的老师给我的要求之一是为应用程序的数据库提供一个“数据字典”。我发现这很有趣，因为我从未听说过这个术语。所以今天我们都要学习什么是数据字典，以及它如何帮助我们处理数据库。我们开始吧。

# 数据字典

> 数据字典是关于数据的信息(如含义、与其他数据的关系、来源、用法和格式)的集中存储库
> 
> *   IBM 计算词典。

“关于数据的信息”是 IBM 定义中最重要的部分。数据字典提供了关于数据的详细信息，比如默认值、数据类型、允许值、属性的含义等等。我们可以称之为文档的优点，因为它为我们提供了关于实体属性的具体细节。

## 关键要素

数据字典包含元素和必要的特性。

*   所有表的名称
*   属性的名称和详细信息(可选/必需，非空，数据类型，长度等)。)
*   权限的详细信息
*   表约束(主键、FK 等)
*   数据库视图
*   关于实现特定属性的函数的注释。

### 举例

| 字段名 | 数据类型 | 字段长度 | 描述 | 例子 |
| --- | --- | --- | --- | --- |
| 身份证明 | 整数 | Ten | 每个员工的唯一 ID | One hundred and forty-three thousand two hundred and twelve |
| 名字 | 文本 | Twenty | 员工姓名 | 安德森 |
| 出生日期 | 日期/时间 | Ten | 员工出生日期 | 08/03/1998 |

## 文档，文档，文档。

现在我们正在理解数据字典，对吗？很明显，一切都与文档有关。我们都知道我们应该为其他开发者而不是为我们自己写文档。这种文档面向数据库管理员、设计人员、开发人员、分析师以及任何有权对数据库执行 SQL 的人。就像你最喜欢的图书馆的文档一样，数据字典提高了开发效率，避免了错误和缺陷，使修改变得容易，改善了团队交流，并给我们带来了一个好的文档的所有好处。当我们处理每天都在增长并且变得越来越复杂的数据库时，我们开始忘记表名、约束、数据库视图、权限等等，这是一个真正的问题。或者，当我们加入一个新团队，我们需要熟悉 DB 关系结构，以开始提高工作效率。
在那个时刻就是我们需要数据字典来救援的时候。

## 如何创建一个？

当我们创建数据字典时，存在不同的工具。选择取决于项目的复杂程度。有一些工具为我们提供了简单的解决方案，它们生成一个. PDF 文件，其中包含所有关于表格和列的信息。另一方面，除了信息矩阵之外，有更多的专业工具为我们提供图表和其他工具来为我们的数据库建模，这些工具更适合大型团队和大型数据库。

一些比较流行的工具是:

*   [Dataedo](https://dataedo.com/)
*   [DBMaster](https://www.dbmaster.io/)
*   [SQL 数据字典](//www.sqldatadictionary.com)
*   [甚至在 PHPMyAdmin](https://dataedo.com/kb/tools/phpmyadmin/how-to-create-data-dictionary)

# 结论

如果你想详细记录你的数据库，最好使用数据字典作为参考。根据需要添加许多细节，并像编写文档一样遵循最佳实践。你未来的自己会欣赏它，你的同事也会欣赏它。