# 数据库模型和实体关系图第五部分

> 原文：<https://dev.to/kevindsteeleii/database-models-and-erds-for-newbs-part-5-3f61>

# CRUD 操作

## 重述

上次我们使用 PostgreSQL 的命令行工具 psql 深入研究了一下 SQL。创建了数据库，制作了表，插入了行。

## 现在怎么办？

这有点非正统，但我正试图平均每周增加 2 篇帖子，所以我在这里结束这个系列。但是，在结束之前，我将回顾一下 SQL 中的一些内容。具体来说，我们如何将行插入到连接表中，创建连接表引用的模型的实例/行，更新一行，最后删除一行并删除整个 dang 数据库。所以我会用它们写一些场景来证明编写代码的合理性。

让我们进入我们的数据库。
[![access database](img/4b68be4d3cb10d229162c4a6eb7ec19d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WzGaEa1I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/N3GCTYq/2019-02-05-001.png) 
由于我们在上一个条目中已经创建了数据库，这是我们使用 psql 访问它的方法。

### 创建行！

场景:伊丽莎白·金教授和詹姆斯·马歇尔教授刚刚被雇佣来教几门课。在确定他们的课程之前，需要将他们输入数据库。
[![insert new professors](img/87493cc23af4b1e38cdbb34dab6863dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dA4h0mph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/bK71Vdz/2019-02-05-002.png) 
**教授们表**:

| 身份证明（identification） | 名字 | 姓氏 |
| --- | --- | --- |
| one | 詹姆斯 | 整理 |
| Two | 伊丽莎白一世 | 国王 |

场景:新聘教授马歇尔和金分别教授英语和数学课程。
[![insert courses](img/b18881ffa3a1bb7979306ab0ed9d523a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YNwxkr98--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/TRnVs9N/2019-02-05-003.png) 
**课程**:

| 身份证明（identification） | 标题 | 描述 | 教授 _id |
| --- | --- | --- | --- |
| one | 大一数学 | 数学的调查... | Two |
| Two | 中古英语 | 中古英语、乔叟及其他 | one |

场景:编号为 1 的学生 Adam Bean 注册了新教授讲授的两门新课程。学号为 2 的 Clara Doe 只注册了数学课，因为她是带着相应的预科转来的。学号 3，埃弗雷特·富兰克林只想读乔叟，所以他注册了马歇尔的课程。
[![insert course registrations](img/6c1dacde2730d1455ea9df30fe66cfe7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bfx3l9dj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/YhVQwgp/2019-02-05-004.png) 
在这里，我们已经插入了教授、课程和课程 _ 注册。请密切注意 course_registrations 表。它只有自己的 id 号和两个外键，一个指向课程行，另一个指向学生。

**课程 _ 注册**:

| 身份证明（identification） | 课程 id | 学生 id |
| --- | --- | --- |
| one | Two | one |
| Two | Two | three |
| three | one | one |
| four | one | Two |

### 从 ID 为 2 的单个学生处读取。

场景 : Clara 需要被查找以向管理员确认一些信息。
T3![select student 2](img/a11f6469c5375aaf517120cf52b7f263.png)T5】

### 更新学生的姓氏。

场景 : Clara 结婚了，她想调整她的学生信息以反映她的合法身份。这就是你如何改变她的名字。
T3![update student 2 last name](img/ae358a448d621961035dfe11b756e135.png)T5】

### 删除一行。

场景 : Everett 想转到一所更适合他的学校，所以他的记录需要被删除。我们删除这样一行:
[![delete student 3](img/7a68ac478f0286e5f173640b7726f2fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--voWgy0Aj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/PGr0xhK/2019-02-05-007.png)

### 丢弃数据库。

**场景**:学校即将关闭，但他们的数据库包含敏感信息，因此需要删除整个数据库。下面的代码是你如何移除或*删除*数据库。
[![drop database](img/b09fbbbe77412cb4e5ce095e06b1fb94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5036v4Y1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/2M54DdV/2019-02-05-008.png)

这下你明白了吧。在过去的 5 个帖子中，我很开心地制作了这些图表，并欢迎任何反馈。感谢阅读！