# 用 Python 过滤大型 CSV 文件

> 原文：<https://dev.to/katiekodes/filter-a-large-csv-file-with-python-3d8j>

Excel 中大文件的问题通常不在于大小本身。而是 Excel 试图向你展示它所有的工作。要修改巨大的 CSV 或 XLSX 文件，如从您的 Salesforce“任务”和“联系人”表导出，请考虑使用 Python 等语言编写代码。

## 为什么 Excel 修改大文件很慢

如果您处理大型电子表格，您可能会因为试图过滤文件和删除某些行而冻结 Excel。

例如，从“ [E for Excel](http://eforexcel.com/wp/downloads-18-sample-csv-files-data-sets-for-testing-sales/) 网站下载文件“ [100000 条销售记录- 3.54 MB](http://eforexcel.com/wp/wp-content/uploads/2017/07/100000-Sales-Records.zip) ”

1.  在 Excel 中打开它。
2.  过滤“国家”，只显示“阿尔及利亚”、“亚美尼亚”、“澳大利亚”和“巴巴多斯”
3.  选择所有可见的线。
4.  右键单击右侧的数字，删除所有可见行。

需要几秒钟，对吧？我的电脑相当棒——从 2017 年开始，配有 4 核 2.50GHz 处理器和 16GB 内存，et Excel 冻结了 6 秒。

然而，并不是删除行降低了 Excel 的速度。

它是在你的屏幕上重新绘制电子表格，吃掉你的电脑。

## Python 可以更快的修改大文件

因为没有图形用户界面的程序可以在不显示它们在做什么的情况下工作，所以一点点 Python 代码可以在不到十分之一秒的时间内完成编辑。

看看这个 Python 脚本 :
的[结果](https://repl.it/@rplrpl/Filter-Large-CSV)

```
import pandas
import datetime

df = pandas.read_csv('100000 Sales Records.csv')

badcountries = ['Algeria','Armenia','Australia','Barbados']

print('There are ' + str(len(df)) + ' rows in the dataset before deleting the "bad" country rows.')
print('Il y a ' + str(len(df)) + ' enregistrements dans les données avant de supprimer ceux des pays non-désirées.')

print()

t1 = datetime.datetime.now()
df = df[~df['Country'].isin(badcountries)]
t2 = datetime.datetime.now()

tm = round((t2 - t1).total_seconds(), 2)
print('It took ' + str(tm) + ' seconds to delete the "bad" country rows.')
print('Il a fallu ' + str(tm) + ' seconds pour suppprimer les données des pays non-désirées.')

print()

print('There are ' + str(len(df)) + ' rows in the dataset after deleting the "bad" country rows.')
print('Il y a ' + str(len(df)) + ' enregistrements dans les données après avoir supprimé ceux des pays non-désirées.') 
```

```
There are 100000 rows in the dataset before deleting the "bad" country rows.
Il y a 100000 enregistrements dans les données avant de supprimer ceux des pays non-désirées.

It took 0.08 seconds to delete the "bad" country rows.
Il a fallu 0.08 seconds pour suppprimer les données des pays non-désirées.

There are 97885 rows in the dataset after deleting the "bad" country rows.
Il y a 97885 enregistrements dans les données après avoir supprimé ceux des pays non-désirées 
```

没有必要选择 Python 作为这类工作的编程语言，但是考虑一种基于代码的方法来转换数据是很重要的。能让机器工作而不需要停下来向你展示它在做什么的东西。

(然而，我确实认为 Python 是一门非常简单的编程语言。)

现在你知道了！