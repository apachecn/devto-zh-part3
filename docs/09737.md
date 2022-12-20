# 如何使用 xlwt 在 excel 文件中写入具体的列数？

> 原文：<https://dev.to/lakhujanivijay/how-to-write-in-specific-number-of-columns-in-excel-file-using-xlwt-2h97>

我有一张单子上面写着

```
po = ['Mon', '6421', 'Tue', '6412', 'Wed', '12416', 'Thu', '23483', 'Fri', '8978', 'Sat', '7657', 'Sun', '6555'] 
```

我希望根据用户提供的器械包数量在 excel 文件中写下这些内容，例如，如果用户说“2”个器械包，那么 excel 中的列表如下所示

```
Mon 6421    Tue 6412
Wed 12416   Thu 23483
Fri 8978    Sat 7657
Sun 6555 
```

因此，实际上，数据填充在 4 个 excel 列中。

* * *

类似地，如果用户说“3”套，那么数据看起来像

```
Mon 6421    Tue 6412    Wed 12416
Thu 23483   Fri 8978    Sat 7657
Sun 6555 
```

因此，实际上，数据填充在 6 个 excel 列中。

* * *

我尝试过类似的东西，但是我不知道用什么来代替`?`和`line#14`。你能帮忙吗？

```
1 import xlsxwriter
2
3 wb = xlsxwriter.Workbook('dem.xlsx')
4 ws = wb.add_worksheet("New Sheet")
5
6 po = ['Mon', '6421', 'Tue', '6412', 'Wed', '12416', 'Thu', '23483', 'Fri', '8978', 'Sat', '7657', 'Sun', '6555']
7 user_set = 4
8
9 num_row  = ((len(po)/user_set) / 2 ) + 1
10
11
12 for row in range(0,num_row):
13    for col in range(0,user_set):
14      ws.write_row(row, col, ? )
15
16 wb.close() 
```