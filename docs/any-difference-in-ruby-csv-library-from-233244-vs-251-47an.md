# Ruby CSV 库从 2.3.3/2.4.4 到 2.5.1 有什么区别吗？

> 原文：<https://dev.to/dharshann/any-difference-in-ruby-csv-library-from-233244-vs-251-47an>

在将一个小型 ruby 应用从 ruby 2.3.3 迁移到 ruby 2.5.1 时，我在用 csv 库解析一个`mechanize`文本 CSV 文件时遇到了问题。这在 ruby 2.3.3 和 2.4.4 中运行良好。

```
csv_header = ["Rep Date", "Or No", "Qw Type", "Sett No", "Order Date", "AS Code", "ZCV", "Amount", "Qty", "BN Code", "AX Code", "User Id", "QW No", "QX Code", "WE No", "MN Code", "KL Name", "IO Id", "A AS", "DD Unit", "DD Amt", "DD Flag", "Remarks", "RRR", "KK Ref No", "TY Type", "SS Regn No", "SR Reg Date", "SU Code", "PPP", "QQQ DEC", "NM Flag", "DP Trans", "O Type"].freeze
converter = lambda { |field, _| field.strip rescue field }
response = agent.submit(form, button)

parsed = CSV.parse(response.body, col_sep: '|', write_headers: true, headers: csv_header, converters: [converter])

# in ruby 2.3.3
parsed.count # 1523

# in ruby 2.5.1
parsed.count # 0

# data in response body looks like bellow
"2018-12-05|8761|T3|9166|2018-12-03|1-R|1JF5|10000.0000|0|11|PBRANCH|88701|2030|489|2312051|4CUM|Kapoor ||41.3500|241.8380|10000.0000|Y||0.0000|66747|SP|3674|24/10/2018|||N|Y|P|NRM|\n2018-12-04|9525|T3|9166|2018-12-03|2-G|1JF5|1000.0000|0|11|BBRANCH|88701|21551|489|545481|VPA|Prasad P||41.3500|24.1840|1000.0000|Y||0.0000|78232|TP|6879|24/09/2018|||N|Y|P|NRM|\n2018-12-04.." 
```