# 应用开发的数据库管理系统:SQL(结构化查询语言)

> 原文：<https://dev.to/leniquenoralez/dbms-for-application-development-sql-structured-query-language-331c>

克里斯·利维拉尼在 [Unsplash](https://unsplash.com) 上的照片

[![Laptop Screen](img/2124afa9626d59a7f9072290306bdf6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M1PEghC0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1518186285589-2f7649de83e0%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1567%26q%3D80)

SQL 是一种编程语言，用于存储、检索和操作数据库中的数据。

SQL 也用于定义数据库的模式。定义表的模式时，您可以指定:

*   表中每个属性的数据类型
*   属性的约束
*   每个表列的名称

SQL 的基本数据类型有:

*   char(字符的缩写)-固定长度的字符串。长度由用户指定。
*   varchar(字符变化的缩写)-是一个可变长度，具有用户定义的最大长度
*   int -一个整数
*   smallint -较小的整数
*   数字-具有用户定义的精度数的定点数。
*   实数、双精度浮点数和双精度浮点数
*   float -具有用户定义精度的浮点数

*int* ，*最小*， *real* 和 *double precision* 数据类型是机器相关的。这些数据类型的最大长度和精度由存储数据库的计算机体系结构定义。

为了在 SQL 中创建表的模式，我们使用 create table 命令。让我们为照片共享应用程序中的一些表格定义模式。

```
CREATE TABLE user(
    user_id INTEGER NOT NULL AUTO_INCREMENT,
    username VARCHAR(30),
    password VARCHAR(30),
    email_add VARCHAR(32),
    fullname VARCHAR(30),
    fb_id INTEGER,
    twitter_id INTEGER,
    profile_pic_url VARCHAR(30),
    privacy_level BOOLEAN,
    tag_option BOOLEAN,
    primary key (user_id, username, email_add),
);

CREATE TABLE followers(
    uid INTEGER
    follower_id INTEGER
    timestamp DATE
    rejectdenyoption INTEGER,
    foreign key (uid) references user
);

CREATE TABLE following(
    uid INTEGER
    follower_id INTEGER
    timestamp DATE
    rejectdenyoption INTEGER,
    foreign key (uid) references user
); 
```

为了将数据插入到我们新创建的表中，我们使用:

```
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...); 
```

我们可以省略列名:

```
INSERT INTO table_name
VALUES (value1, value2, value3, ...); 
```

但是我们必须按照表模式中定义的顺序放置值。

让我们向用户表中添加一些记录。

```
INSERT INTO table_name (username, password, email_add, fullname, fb_id, twitter_id, profile_pic_url, privacy_level, tag_option)
VALUES (Jame_Bond_007, fakepassword, 'bond_james@hotmail.com' , 'Jame Bond' , 1847123, 1847123, 'https://cdn.photoshare.com', true, false);

INSERT INTO table_name (username, password, email_add, fullname, fb_id, twitter_id, profile_pic_url, privacy_level, tag_option)
VALUES (LilLeafHurricane, fakepassword, 'rocklee@gmail.com' , 'Rock Lee' , 3547126, 3547126, 'https://cdn.photoshare.com', true, false; 
```

现在我们的表中有了数据。我们需要检索这些数据，以便在我们的应用程序中显示出来。查询是从我们的数据库中获取数据的请求。一个 SQL 查询由三部分组成， **select** ， **from** ，以及 **where** 。来自子句的**告诉我们正在查询哪些表。 **select** 子句告诉我们希望从查询结果中输出哪些列。 **where** 子句用于设置查询输出的条件。**

让我们在照片共享应用程序数据库上做一些查询。

```
-- Select all user with private account
select username, email_add, profile_pic_url from user where privacy_level = true;

-- Get a specific user data
select * from user where username = 'username';

-- Get all user photo [Return only photo_id],user id is random
select photo_id from photo where uid = 98494

-- Get a specific user follower [Return follower id]
select follower_id from followers where uid = 64736 and follower_id = 12342

-- Get a specific user follower [Return follower id]
select follower_id from followers where uid = 64736 and follower_id = 12342 
```

SQL 提供聚合函数，聚合函数将集合作为输入并返回单个值。

*   平均值:avg
*   最小值:min
*   最大值:最大
*   总计:总和
*   计数:计数

在所有聚合函数中，sum 和 avg 只能输入数字。

下面是几个使用聚合函数的例子。

```
-- Count all likes on a specific photo
select sum (photo_id) as likes from likes where photo_id= 090982;

-- Count all user followers
select sum (uid) as likes from followers where uid = 090982;

-- Count all people who user is following
select sum (uid) as likes from following where uid = 090982; 
```

其他聚合函数有很多用例，但为了保持示例简单，我使用了 sum，因为它在本应用程序中的用例非常简单。

SQL 提供了集合运算 intersect、union 和 except，允许您对两个表执行集合运算。union 操作将两个查询的结果合并到一个表中。任何重复都会被删除。intersect 操作接受两个查询的结果，并且只返回两个查询结果中的记录。except 操作获取查询结果的唯一行，并返回没有出现在第二个结果集中的行，except 操作不删除重复的行。

以下是使用 intercept 获取两个用户共享的所有喜欢的照片的 set 操作示例:

```
-- Return the photo_id of all the images two user both like
SELECT photo_id
FROM   likes
WHERE  likerUID = 12345;
INTERSECT
SELECT photo_id
FROM   likes
WHERE  likerUID = 67890; 
```

SQL 是一种用于与数据库中的数据进行交互的编程语言。SQL 有称为聚合函数的特殊函数，您可以使用它来应用于查询结果，以返回单个输出。您可以对多个查询的结果执行集合操作，以合并成一个结果。

#### 有用资源:

*   [SQL -初学者完整课程](https://www.youtube.com/watch?v=HXV3zeQKqGY)