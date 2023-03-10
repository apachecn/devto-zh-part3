# SQL 工作流:Visual Studio 代码+代码段

> 原文：<https://dev.to/alexantra/sql-workflow-visual-studio-code-snippets-chb>

我喜欢 Visual Studio 代码，也喜欢寻找加快工作流程的方法。

作为一名 SQL 分析师，像 intellisense 和 autocompletion 这样的东西有很大的帮助，它们提高了我的编码速度，减少了我写的错别字数量，并且总体上保持了我的代码在脚本之间的一致性。

在以前的工作中，我能够利用本地应用程序进行编码，比如 Oracle Developer。这些应用程序会自动创建一个智能感知库，并允许我定制代码片段。

在我目前的角色中，不是一个特别的选择。我们使用红移，它是通过远程桌面连接和一个名为 Aginity 的应用程序访问的。这个解决方案不像我习惯的那样成熟。远程机器有明显的毫秒级延迟，Aginity 是一个非常简单的应用程序。

这导致我在本地机器上使用 Visual Studio 代码编写所有 SQL 脚本，然后在需要运行命令时将它们复制到 Aginity 中。

但是我错过了智能感知....

所以我开始复制一个智能感知库，考虑了两个关键因素。第一，由于安全原因，它不能直接连接到 Redshift，第二，我必须能够与我的团队中任何想要使用它的成员共享该库和任何更新。

第二个因素直接影响了 VSC 自己的代码片段库，这是一个 JSON 文件，你可以随意填充。但是，它存储在本地，这意味着它不能被多个 VSC 实例同时访问。

所以我去扩展商店看了看，发现了一个 Oracle SQL 扩展，它允许您在自己选择的位置拥有一个智能感知和代码片段库。作为一家谷歌公司，这个解决方案很有效，因为这意味着我可以将 JSON 文件存储在我们的谷歌硬盘上，每个人都可以访问它。

是的，我知道你在想什么，红移的甲骨文扩展？红移扩展可以做到这一点的可能性微乎其微，Oracle 在语法突出显示和功能方面已经足够接近了。在我的经验中，唯一不起作用的是“跳转到表创建”功能，因为我们编写了“创建表，如果存在”，而 oracle 扩展认为我们所有的表都被称为“如果存在”！！！

所以下一个问题是:我如何填充代码片段和智能感知库。

片段库很简单，我只需要用我选择的预制代码填充这个库。我有一些我们经常使用的 SQL 代码，我还找到了十个最常用的列名，并为每个列名做了一个代码片段。

例如，它的语法是

"代码段名称":{ "前缀":"您将键入什么"，"正文":["显示什么"]，"描述":"插入您想要的任何内容" }，

例如

```
"Car ID":{"prefix":"ca","body":["cardid ="],"description":"generic column (car id)"}, 
```

Enter fullscreen mode Exit fullscreen mode

所以当你开始输入 ca 时，它会自动填充‘cardid =’

太棒了，这是成功的一半。现在来填充我的智能感知库。该扩展有一个单独的 JSON 文件供您填写您的表结构，它看起来像这样:

```
{  "Table Name"{  "Column Name",  "Column Name",  "Column Name"}  } 
```

Enter fullscreen mode Exit fullscreen mode

虽然我知道如何从我们的 DW 中提取表结构，但我不知道如何将数千个表和数百万个列放入每个表的 JSON 包中。

我知道这是可以做到的，我只是缺乏技巧。然而，这并不需要。我发现，通过测试，它不适合的目的。

智能感知查找只想要做一个层次的深度，所以如果你有数据库，然后是模式，然后是表，它就不起作用了。

所以我放弃了这个想法...回到我的片段库。它适用于我的代码片段，所以为什么不为每一个表都制作一个代码片段呢？

我就是这么做的。我很务实，只为我们使用的核心模式编写了片段，这仍然带来了多达 1000 行的片段！

然而，我的第一关遇到了一些问题。

我有同名的表，代码片段文件只需要唯一的名称，所以我主要是手动清理它们，在末尾附加模式名。我手动这么做是因为太少了。如果有更多，我会把所有的命名都变成 table_name(模式名)。

我们的一些表名有很大的前缀，比如 schema . name _ of _ source _ system _ database _ then _ table _ name，所以代码片段库很难找到这些前缀。

例如，如果您的表名为“sat . cars_made_in_europe_failed_tests ”,并且您将它称为“failed_tests”表，则键入“failed _ tests”将不起作用，您必须键入“cars _ made _ in _ Europe _ f ”,代码段库才能理解。

因此，我再次提取所有数据，使用一些自定义的 case 语句和子字符串，将前缀(人们键入的位)更改为“failed_tests ”,然后将数据库名称放入描述字段，以防有两个“failed_tests”数据库(其中一个可能用于美国)。片段的实际主体没有更改，一旦片段知道您在做什么，它仍然会插入“sat . cars _ made _ in _ Europe _ failed _ tests”

我还必须开始为我的 snippets 前缀考虑命名约定。在大多数情况下，我只是将列名设置为列名的前 5 个字符。

因此，对于一个名为 car_start_date 的列，前缀应该是 car_s，这是可行的，因为我们有像 car_end_date 这样的内容，它变成了 car_e，car_version 变成了 car_v。

然而，对于大块的代码，我选择在所有前缀前面附加“q”。

原因是，如果我有一个名为“all_cars”的表，并且我有一段我经常在该表上使用的代码，该代码片段和该表最终将具有相同或相似的名称。因此，如果我想插入表名，我键入“all_ca”，但如果我想插入代码块，我键入“qall_cars”。这避免了任何冲突。Q 也是一个未被充分利用的字母，在我的脑海中我称它为“生活质量”:

所以是的，这就是我现在的处境。我已经改进了 6 个月，这对我的工作流程有很大的好处。我为我的团队创建了一些文档，以便他们可以自己设置，我还列出了他们可以输入的所有快捷方式代码，以获得特定的列或代码块。

这不是什么高科技，在未来的某个时候，我需要为新的表更新它，但这是一个简单的解决方案，让我受益匪浅。

注意:你必须将你的文件保存为。在 sql 片段开始工作之前。你不能打开一个新的未保存的 VSC 文件。

## 技术性的东西

### 分机

名称:语言 PL/SQL
Id:XYZ . plsql-Language
描述:PL/SQL 语言(Oracle)支持
版本:1.8.1
发布者:xyz
VS 市场链接:[https://marketplace.visualstudio.com/items?itemName=xyz.plsql-language](https://marketplace.visualstudio.com/items?itemName=xyz.plsql-language)

### 你是怎么得到表名的

几乎所有的数据库都有一个表，其中列出了所有可用的表名。在 oracle 中它被称为 *DBA_Objects* ，在 TSQL 中它被称为 *Sys.objects* ，在 Redshift 中它被称为 *pg_table_def*

确保您可以访问这些表，您可能需要与您的 DBA 联系以获得访问权限。

有趣的事实:在红移中，你需要设置你的搜索路径。例如，如果您刚刚从 pg_table_def 执行 select distinct(tablename ),您将只能获得公共和/或目录模式中的表名。要让它查看特定的模式，您需要在 select 语句之前执行以下代码:

```
set search_path to ‘$user’,’schemaname’;commit; 
```

Enter fullscreen mode Exit fullscreen mode

这确实意味着，如果您有多个红移模式，您将需要为每个模式运行一次查询。

然后，您需要调整 select 语句以输出正确的 JSON 语法。从我做的红移:

```
set         search_path to '$user','<schemaname>'
;
select '"'||tablename||'":{"prefix":"'||tablename||'"'||',"body":["<schemaname>.'||tablename||'"],"description":"<insert useful description here>"},'
from            (
        select  distinct(p.tablename)
        from    pg_table_def p 
        where   p.schemaname not in ('pg_catalog','public')
)
group by    tablename
order by    tablename; 
```

Enter fullscreen mode Exit fullscreen mode

如需更多帮助，请参阅 Helens 关于[系统表](https://dev.to/helenanders26/sql-201-6-ways-to-search-with-system-tables-h0)的文章。

任何问题让我知道！