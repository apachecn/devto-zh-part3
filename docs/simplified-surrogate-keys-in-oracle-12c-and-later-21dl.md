# Oracle 12c 及更高版本中简化的代理键

> 原文：<https://dev.to/geekcyclist/simplified-surrogate-keys-in-oracle-12c-and-later-21dl>

在我的工作中，我主要在微软堆栈中工作，但是我必须频繁地切换 DBMS 后端。不幸的是，我发现有时很难跟上变化，重要的更新与我擦肩而过。这是一个已经存在几年的产品，但是在我的主要关注 SQL Server 的团队中没有人知道。

在 Oracle 12c 之前，没有对自动编号或标识列的内置支持，而其他几个 RDMBS 产品都有这种功能。相反，模式是创建一个序列/触发器对，并依赖该对来管理列中的值。

在 12c 版中，Oracle 现在支持两个类似行为的选项。表格可以用一列定义为`NUMBER GENERATED ALWAYS AS IDENTITY`，以自动生成系统链接序列。或者，可以用带有`DEFAULT ON NULL [sequence].NEXTVAL`的列定义一个表。

## 使用标识作为代理键的表

### 创作

命令:

```
CREATE TABLE default_test
    ("ID" NUMBER GENERATED ALWAYS AS IDENTITY, 
    "DESCRIPTION" VARCHAR2(50 BYTE) NOT NULL ENABLE
); 
```

结果是使用 SQL Developer 生成的 SQL 创建的表:

```
CREATE TABLE "[owner]"."DEFAULT_TEST"
   ("ID" NUMBER GENERATED ALWAYS AS IDENTITY
            MINVALUE 1 MAXVALUE 9999999999999999999999999999
            INCREMENT BY 1 START WITH 1
            CACHE 20 NOORDER NOCYCLE  
            NOT NULL ENABLE,
   "DESCRIPTION" VARCHAR2(50 BYTE) NOT NULL ENABLE
   ) SEGMENT CREATION DEFERRED 
   PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255 
   NOCOMPRESS LOGGING
   TABLESPACE "[owner]" NO INMEMORY ; 
```

查看列显示

| 列名 | 数据类型 | 可空的 | 数据 _ 默认值 |
| --- | --- | --- | --- |
| 身份证明 | 数字 | 不 | “[所有者]”。“ISEQ$$_246189”。nextval |

请注意，Oracle 为您创建了一个系统生成的序列，而不是传统的触发器/序列对。然后，它将该行的默认值设置为系统生成序列的`nextval`。

### 删除

如果你想删除这个表，在这种情况下序列也同时被删除。

正在执行:

```
DROP TABLE default_test PURGE; 
```

移除表和自动生成的序列(并且`PURGE`将它们从回收区移除，使它们不可用于闪回-小心)。

### 附加细节

#### 查看身份栏详细信息

您可以通过执行:
来查看附加到表的标识列的列表

```
SELECT table_name,
       column_name,
       sequence_name,
       generation_type,
       identity_options
FROM all_tab_identity_cols
WHERE owner = '[owner]'
ORDER BY 1, 2; 
```

样本结果

| 表名 | 列名 | 序列名称 | 产生 | 身份选项 |
| --- | --- | --- | --- | --- |
| APP_CONFIG | 身份证明 | ISEQ 84588 美元 | 总是 | 从 309 开始，增量为 1，最大值… |
| 应用程序配置类型 | 身份证明 | ISEQ 84590 美元 | 总是 | 开始于:3，增量为:1，最大值… |

#### IDE 支持

SQL developer 17.3 . x 版似乎不支持在表创建对话框中创建标识列。用户必须手动编辑表创建脚本来创建标识列。

## 对代理键使用缺省 ON NULL 的表

使用显式序列和 Oracle 12c enhanced `DEFAULT`关键字可以实现类似于上述身份用法的行为。

### 创建时使用缺省 ON NULL

正在执行:

```
CREATE SEQUENCE seq_default2_test_id;

CREATE TABLE default_test2 
   ("ID" NUMBER DEFAULT ON NULL SEQ_DEFAULT2_TEST_ID.NEXTVAL,
   "DESCRIPTION" VARCHAR2(50 BYTE) NOT NULL ENABLE
); 
```

将创建一个序列、一个表，并将使 ID 字段的行为类似于自动编号或标识字段。

SQLDeveloper IDE 为此表生成的 SQL 是:

```
CREATE TABLE "[owner]"."DEFAULT_TEST2"
   ( "ID" NUMBER 
            DEFAULT ON NULL "[owner]"."SEQ_DEFAULT2_TEST_ID"."NEXTVAL"
            NOT NULL ENABLE,
        "DESCRIPTION" VARCHAR2(50 BYTE) NOT NULL ENABLE
   ) SEGMENT CREATION DEFERRED
   PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255
   NOCOMPRESS LOGGING
   TABLESPACE "[owner]" NO INMEMORY ; 
```

请注意，在这两种情况下，默认情况下都不会生成显式 primary key 约束。

#### 默认与空时默认

如果仅使用`DEFAULT`而不是`DEFAULT ON NULL`来链接序列，则有两个显著差异:

1.  该字段不会自动指定为`NOT NULL`
2.  显式地将`NULL`传递给 ID 列的插入(ORMs 生成的`INSERT`语句经常出现这种情况)将会成功，并可能导致引用完整性错误或下游数据完整性问题。

在 SQLDeveloper 中查看表列会显示:

| 列名 | 数据类型 | 可空的 | 数据 _ 默认值 |
| --- | --- | --- | --- |
| 身份证明 | 数字 | 不 | “[所有者]”。“SEQ _ 默认 2 _ 测试 _ID”。" NEXTVAL " |

### 删除默认序列

在这种情况下，删除表不会自动删除序列。也可以删除序列而不删除表，如果 insert 不包含 ID 列的值，由于对序列的依赖性，这将导致运行时错误。

```
SQL Error: ORA-02289: sequence does not exist 
```

要删除这两个对象，请执行:

```
DROP SEQUENCE seq_default2_test_id;
DROP TABLE default_test2 PURGE; 
```

## 为什么要切换？

下面列出的参考资料中的文档指出

> 毫不奇怪，[基于触发器/序列的表]的性能比其他表差得多。直接使用序列[使用`DEFAULT` ]和 12c 标识列给出了可比较的结果，通常比使用触发器填充 ID 列快一个数量级。[链接](https://oracle-base.com/articles/12c/identity-columns-in-oracle-12cr1#performance)

如果您在很少执行插入的情况下使用小表，那么坚持使用触发器/序列对是没问题的，并且对于使用过早期 Oracle 版本的开发人员来说很容易理解。

但是，如果您对规模和负载有任何顾虑，我建议您改用这两种较新技术中的一种。

## 如何选择使用哪种技术

在以下情况下使用`"ID" NUMBER GENERATED ALWAYS AS IDENTITY`:

*   您无需直接访问序列(参见下面`DEFAULT`的相关项目)
*   您希望忽略序列的创建，让 DB 自动处理它。
*   你不介意你的模式中充满了像 ISEQ$$_219376 这样的序列
*   如果您删除表，您希望 DB 自动处理序列的删除。

在以下情况下使用`"ID" NUMBER DEFAULT ON NULL [sequence_name].nextval`:

*   您需要访问返回的序列值，作为 API、业务层或 ORM 中多步流程或事务的一部分，并且要手动插入它。在这种情况下，您可以使用符合您的组织命名约定的序列。
*   您对序列有严格的命名约定，并希望避免自动生成的序列名称。
*   您需要确保开发、测试和生产模式之间的序列名称是相同的。
*   如果其中一个对象被删除，您可以管理这两个对象的删除或修改。

## 参考文献

*   [Oracle Database 12c 第 1 版(12.1)中的身份列](https://oracle-base.com/articles/12c/identity-columns-in-oracle-12cr1)
*   [表列的默认值:Oracle Database 12c Release 1 (12.1)中的增强功能](https://oracle-base.com/articles/12c/default-values-for-table-columns-enhancements-12cr1)
*   [改进了 Oracle Database 12c 的默认值](https://blogs.oracle.com/oraclemagazine/improved-defaults-in-oracle-database-12c)