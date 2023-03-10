# 函数和复合类型如何改变我使用 PostgreSQL 的方式

> 原文：<https://dev.to/tommydreamer57/how-functions-and-composite-types-changed-the-way-i-use-postgresql-7a2>

在编码训练营期间，我已经学习了足够的 SQL 来创建一些表和执行最简单的 CRUD 操作，当我了解 Postgres 中的复合类型时，我被震惊了。

我最初了解到 SQL 查询总是返回一个列表，所以如果您想从数据库中返回一个类似 JSON 的结构，您要么必须执行多个 SQL 查询并将结果在 Node(或您使用的任何其他语言)中缝合在一起，要么您必须使用 GraphQL 之类的东西，它会自动生成您需要的 SQL 并以您指定的格式构造它。

但是，使用复合类型(如果我没弄错的话，这是一个只存在于 Postgres 中的特性)，不仅可以从查询中返回类似 JSON 的结构，还可以接受类似 JSON 的数据作为操作的输入。

请注意，我不是在谈论以复合类型存储数据(它有自己更有限的用例)，而是在谈论使用复合类型作为查询的输入和输出。

## 样本模式

我要举的例子是一个表示学校班级的模式。

这里有一个简单的模式来捕获班级、学生、教师和注册:
(注意学生和教师是如何存储在表“people”中的)

### 表格

```
CREATE TABLE people (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);

CREATE TABLE classes (
    id SERIAL PRIMARY KEY,
    subject VARCHAR(50),
    teacher_id INTEGER REFERENCES people
);

CREATE TABLE student_class_enrollments (
    student_id INTEGER REFERENCES people,
    cid INTEGER REFERENCES classes,
    UNIQUE (student_id, cid)
); 
```

### 样本数据

让我们创建一个类，这样我们就有东西要查询:

```
-- create a teacher and some students
INSERT INTO people (
    first_name,
    last_name
)
VALUES
('Tommy', 'Lowry'), -- id 1
('Student', 'One'), -- id 2
('Student', 'Two'), -- id 3
('Student', 'Three'); -- id 4

-- create a class
INSERT INTO classes (
    subject,
    teacher_id
)
VALUES
('Postgres Composite Types', 1);

-- enroll students in the class
INSERT INTO student_class_enrollments (
    student_id,
    cid
)
VALUES
(2, 1),
(3, 1),
(4, 1); 
```

## 查询:老办法

现在，如果我想检索一个类的所有信息，我以前会写一个类似这样的查询:

```
SELECT * FROM classes c
LEFT JOIN people t ON t.id = c.teacher_id
LEFT JOIN student_class_enrollments e ON e.cid = c.id
LEFT JOIN people s ON s.id = e.student_id 
```

这将为我提供如下数据:

| 类别 id | 科目 | 教师 id | 教师名字 | 教师姓氏 | 学生证 | 学生名字 | 学生姓氏 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| one | 邮政... | one | 托马斯 | 劳瑞 | one | 学生 | 一个 |
| one | 邮政... | one | 托马斯 | 劳瑞 | Two | 学生 | 二 |
| one | 邮政... | one | 托马斯 | 劳瑞 | three | 学生 | 三 |

了解我们如何拥有冗余的非结构化数据？不仅每个学生的班级和教师被表示一次，而且我们的列表也缺少对数据深度的任何表示——班级、教师和学生在列表的每个节点中都被集中在一起，而不是被组织到一个层次结构中。

## 复合类型

让我们看看复合类型如何帮助我们将这些数据格式化成更合理的结构。

**注意**如果你不熟悉复合类型，复合类型是由其他类型“组成”的类型。它允许您拥有一个可能包含其他项目的单个项目——本质上它允许一个嵌套结构。复合类型可以由内置 SQL 类型以及其他复合类型组成。

您可以在官方文档中阅读更多关于 PostgreSQL 复合类型的内容。

为了向您展示这是如何工作的，让我们创建一些反映我们的数据结构的复合类型:

```
-- A `person` is composed of an `integer` and two `varchar`s titled 'id', 'first_name', and 'last_name', respectively.
CREATE TYPE person AS (
    id INTEGER,
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);

-- Likewise, a `class` is composed of an `integer`, a `varchar`, a `person`, and a `person[]` array, titled 'id', 'subject', 'teacher', and 'students', respectively.
CREATE TYPE class AS (
    id INTEGER,
    subject VARCHAR(50),
    teacher PERSON,
    students PERSON[]
); 
```

请注意，这种结构与我们的表有些相似，但有几个主要区别:

1.  代替一对多关系(单个外键)，我们简单地在一个类中嵌套一个教师。
2.  代替多对多关系(连接表)，我们简单地在一个类中嵌套一组学生。

**注**

## 查询:新方式

现在，我们准备创建一个函数，该函数将输出我们使用复合类型定义的结构中的整个类。

## 功能签名

这需要几个步骤，所以让我们从一个函数签名开始，它接受一个类 id 并返回一个类:

```
CREATE OR REPLACE FUNCTION read_entire_class(cid INTEGER)
RETURNS class AS $$
DECLARE
BEGIN
END;
$$ LANGUAGE plpgsql; 
```

## 变量

现在我们需要定义我们将使用的变量，以便检索每个键/节点来构建我们的复合类:
(一旦我们实际使用它们，这将更有意义)

```
CREATE OR REPLACE FUNCTION read_entire_class(cid INTEGER)
RETURNS class AS $$
----------
DECLARE
    subject TEXT;
    teacher PERSON;
    students PERSON[];
----------
BEGIN
END;
$$ LANGUAGE plpgsql; 
```

## 构造复合值/显式类型转换

接下来，在我们进入难点之前，让我们添加我们的 return 语句。这里我们将使用`ROW()`函数，以及显式类型转换(`::class`)来将我们的类放在一起并返回它。乍一看，这似乎很抽象，但实际上，复合类型就是这样:一行字段名称和相应的数据类型。

```
CREATE OR REPLACE FUNCTION read_entire_class(cid INTEGER)
RETURNS class AS $$
DECLARE
    subject TEXT;
    teacher PERSON;
    students PERSON[];
BEGIN

----------
    RETURN ROW(     -- ROW() function to create composite
        id,         -- these must be in correct order
        subject,
        teacher,
        students
    )::class;       -- explicit type casting
----------
END;
$$ LANGUAGE plpgsql; 
```

请注意，所用值的顺序很重要，必须始终与类型声明中的顺序相同。如果你把它们按错误的顺序排列，并且类型不匹配，当你运行这个函数时，它会抛出一个错误。更糟糕的是，如果您将它们按错误的顺序排列，并且类型是兼容的，那么您将在查询中默默地不匹配键，直到您意识到有什么不对。

根据文档，只要复合类型有不止一个字段，实际上可以省去关键字`ROW`。为此，您可以删除`ROW`并保留括号。您也可以省去显式类型转换，因为 SQL 会自动将返回转换为函数声明的返回类型。

现在你应该能够创建并运行这个函数了——但是每次你都会得到一个空类。

## 用查询结果填充复合值

现在剩下的就是用数据库中的查询结果填充数据结构。

我们将一步一步来，假设你对这门语言了解不多。

### 主题

首先，我们来了解一下主题:

```
CREATE OR REPLACE FUNCTION read_entire_class(cid INTEGER)
RETURNS class AS $$
DECLARE
    subject TEXT;
    teacher PERSON;
    students PERSON[];
BEGIN

----------
    SELECT c.subject FROM classes c   -- selects `subject` from classes
    INTO subject                      -- assigns query result to variable `subject`
    WHERE c.id = cid;
----------

    RETURN ROW(
        cid,
        subject,
        teacher,
        students
    )::class;
END;
$$ LANGUAGE plpgsql; 
```

`SELECT ... INTO`将把查询结果赋给任何指定的变量，这样我们可以在以后引用结果。

### 老师

现在，让我们去找老师:

```
CREATE OR REPLACE FUNCTION read_entire_class(cid INTEGER)
RETURNS class AS $$
DECLARE
    subject TEXT;
----------
    tid INTEGER; -- add teacher id variable
----------
    teacher PERSON;
    students PERSON[];
BEGIN

    SELECT c.subject FROM classes c
    INTO subject
    WHERE c.id = cid;

----------
    -- EITHER --------
    SELECT t.id, t.first_name, t.last_name FROM people t
    INTO teacher
    WHERE t.id IN (                             -- subquery to select correct teacher
        SELECT c.teacher_id FROM classes c
        WHERE c.id = cid
    );
    -- OR --------
    SELECT c.teacher_id FROM classes c          -- extra query to get `teacher_id` instead of using subquery
    INTO tid
    WHERE c.id = cid;

    SELECT t.id, t.first_name, t.last_name FROM people t
    INTO teacher
    WHERE t.id = tid;
    -- END OR ----
----------

    RETURN ROW(
        cid,
        subject,
        teacher,
        students
    )::class;
END;
$$ LANGUAGE plpgsql; 
```

这里没有太多新的东西，只是一个潜在的子查询来获取类的老师。我将保留子查询，因为它更简洁。

### 学生

现在我们只需要让学生们。这个有点难，因为我们要处理一份学生名单。你可以这样做:

```
CREATE OR REPLACE FUNCTION read_entire_class(cid INTEGER)
RETURNS class AS $$
DECLARE
    subject TEXT;
    teacher PERSON;
    students PERSON[];
BEGIN

    SELECT c.subject FROM classes c
    INTO subject
    WHERE c.id = cid;

    SELECT t.id, t.first_name, t.last_name FROM people t
    INTO teacher
    WHERE t.id IN (
        SELECT c.teacher_id FROM classes c
        WHERE c.id = cid
    );

----------
    SELECT ARRAY_AGG(ROW(s.id, s.first_name, s.last_name)::person) FROM people s
    INTO students
    WHERE s.id IN (
        SELECT e.student_id FROM student_class_enrollments e
        WHERE e.class_id = cid
    );
----------

    RETURN ROW(
        cid,
        subject,
        teacher,
        students
    )::class;
END;
$$ LANGUAGE plpgsql; 
```

`ARRAY_AGG()`获取查询结果并将它们转换成一个数组，因此它们与 students 变量的数组类型兼容。`ROW(...)::person`将每个学生转化为复合人。

## 搞定！

现在我们有了一个函数，只需要一个类 id 就可以将一个类及其教师和学生捆绑到一个漂亮的、嵌套的、类似 JSON 的结构中。

为了调用函数，运行`SELECT * FROM read_entire_class(1)`并查看漂亮的嵌套结果！