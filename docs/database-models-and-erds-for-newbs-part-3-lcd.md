# 数据库模型和实体关系图第三部分

> 原文：<https://dev.to/kevindsteeleii/database-models-and-erds-for-newbs-part-3-lcd>

## 回顾上一次

上一次，我们看了一个有 3 个表的模拟学校注册表:学生、教授和课程注册的连接表。我也答应做一些编码，这就是我的打算。所以我们开始吧！

还记得这个吗？
[![has many through](img/1eed06c77d33a16c4322ca95639c733a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--2XtZGc-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/N2dmN9n/has-many-through.jpg)

那么这有什么问题呢？

*   课程注册只将学生和教授联系起来(没有课程可看)
*   我们需要记录班级/课程本身及其 id 号、名称等
*   您需要能够为各个班级生成学生花名册

## 修复问题

这是我想到的。
[![updated has many through](img/e8d35ebc0ae9ec1e41483d49e3ee56fb.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--xwGZXSaY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/BgLRdfM/many-to-many-2.jpg)

因此，我们在以下方面修改了模型及其关系:

*   这样一来，一门课程**就属于一位教授了**
*   课程注册**属于**课程和学生
*   一个课程注册实体**通过其**所属的课程**拥有**一名教授

接下来，我们将看到课程注册表中的一行与以前相比是什么样子

之前:

| 身份证明（identification） | 教授 _id | 学生 id |
| --- | --- | --- |
| One hundred and twenty-three | Thirty-seven thousand four hundred and ninety-four | Seven hundred and forty-six million three hundred and eighty-four thousand six hundred and eighty-four |

之后:

| 身份证明（identification） | 课程 id | 学生 id |
| --- | --- | --- |
| One hundred and twenty-three | Seven million eight hundred and ninety-three thousand seven hundred and twenty-six | Seven hundred and forty-six million three hundred and eighty-four thousand six hundred and eighty-four |

为什么都是身份证号码？
*高基数*是 SQL 数据库的理想选择，这个解决方案比以前的版本更能提升高基数。出于几个原因，存储对模型的引用比克隆数据库模型更合理。尤其是当你需要更新或者删除一个模型的时候。如果您克隆而不是使用引用，您将不得不在多个地方重复相同的操作，这将导致错误，因此使用引用来代替。

引入外键
我的示例中使用的引用是 course_id 和 student_id，它们有一个特定的名称，**外键**。外键不需要以任何特定的名称命名，但它应该是有意义的，因此当我在课程注册表中创建一行时，我将该列命名为课程模型的 id 号 course_id，并对 student_id 使用相同的名称。

所以，现在就这样，但是下次当我最后抛出一些代码来展示我将如何设置这些表时，再对它进行调整。