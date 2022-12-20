# POSTGRESQL 存储函数(PL/pgSQL)入门

> 原文：<https://dev.to/samuyi/a-primer-on-postgresql-stored-functions-plpgsql-1594>

Postgresql 函数扩展了 sql 语言；它增加了编程语言中常见的功能，如控制语句和循环，使应用程序开发人员的生活更加轻松；它们完全在数据库服务器上存储和执行。使用函数意味着你不会写出无效的代码，这将成为你的应用程序的瓶颈。比方说，你需要从数据库中获取一些数据进行计算，根据计算的结果，你需要获取一些额外的数据进行更多的计算，并将结果存储在数据库中。这将需要多次调用数据库，如果数据库服务器与应用程序服务器位于不同的主机上，情况会更糟，网络调用会增加流程的执行时间。像前面描述的复杂逻辑可以放在一个函数中，在数据库服务器上一次执行，去掉所有不必要的中间网络调用。

## PL/pgSQL

Postgresql 在定义函数方面非常灵活，它允许你用几乎任何你喜欢的编程语言来定义函数。有一个用于 [python](https://www.postgresql.org/docs/9.6/plpython.html) 、 [R](http://www.joeconway.com/plr/doc/doc.html) 、 [javascript](https://plv8.github.io/) 的模块，包括但不限于 [Java](https://tada.github.io/pljava/) 。默认情况下，该语言的模块不会安装在数据库中。另一个用于编写函数的模块是用于编写函数的 Pl/pgSQl 模块，它基于 SQl 语言，从版本 9 开始默认安装在数据库中。PL/pgSQL 函数看起来是这样的:

```
 CREATE FUNCTION test_func(integer, text) RETURNS integer
   AS $$
     /* function body text  goes here */
   $$
   LANGUAGE plpgsql; 
```

基本上，我们用 CREATE FUNCTION 语法定义了一个名为 test_func 的函数。测试函数接受两个参数，一个整数和一个文本，我们指定返回一个整数。我们用 AS 表示函数体的开始,$$表示函数体开始和结束的引用。函数体的引号不必是$$它可以是常规的单引号或双引号，但是如果后两者中的任何一个在函数体中使用；他们必须逃跑。最后，我们定义了使用关键字语言的过程语言；在我们的例子中，它是 plpgsql。

功能体采用块的形式；该块定义如下:

```
 [ << label >> ]
       [ DECLARE
         declarations ]
       BEGIN
        statements
       END [ label ]; 
```

'<< label >> '代表一个块的句柄，它们是可选的。可以嵌套块，因此标签充当块的参考。顾名思义，DECLARE 用于声明块中使用的变量。开头和结尾包含了函数的主要逻辑。函数声明的通用 DDL 语法有更多的选项，你可以在这里查看。

举个例子，假设我们有一个管理汽车销售组织的应用程序；跟踪新车、员工和销售。下面给出了应用程序的表格。