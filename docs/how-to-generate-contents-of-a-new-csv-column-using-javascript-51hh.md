# 如何使用 JavaScript 生成新 CSV 列的内容

> 原文：<https://dev.to/chiamakaikeanyi/how-to-generate-contents-of-a-new-csv-column-using-javascript-51hh>

在迁移之后，我需要在数据库表中填充数据，以更新生产数据库模式。我想到了不同的方法来达到这个目的。手动用数据填充行不是一个选项，因为数据库包含数千条记录。

我考虑了两种方法:

*   使用正则表达式
*   编写代码来实现它

过了一段时间，我满足于第二种方法，因为在运行正则表达式时存在访问限制。

以下是我是如何做到的:

*   我导出了数据，
*   编写 JavaScript 代码来生成新列的内容
*   创建一个临时表并导入新生成的 CSV 数据
*   使用内部联接将数据导入到实际的表中

一个样本数据

```
"id", "label"
1,"Name"
2,"Age"
3,"Gender"
4,"Date of birth" 
```

Enter fullscreen mode Exit fullscreen mode

我将数据赋给了一个变量

```
var data = `"id", "label"
1, "Name"
2,"Age"
3,"Gender"
4,"Date of birth"` 
```

Enter fullscreen mode Exit fullscreen mode

最初，我使用嵌套的`for loop`实现了这个结果。考虑到性能，我最终重构了代码，只使用了一个`for loop`

生成新列
内容的 JavaScript 代码

```
const dataAsArray = data.split('\n');

for(let datum of dataAsArray) {
  let currentRow = datum.split(',');
  let newColumn = currentRow[1].trim().toLowerCase().split(' ').join('_');
  currentRow += `,${newColumn}`;

  console.log(currentRow);   
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码生成了我需要的数据。即第二列的内容，没有空格，用下划线分隔。我将结果保存在 CSV 文件中。

然后，我创建了一个包含列`"id","label","code"`的临时表，并使用命令
加载数据

```
LOAD DATA LOCAL INFILE '/Users/Chiamaka/Desktop/query_result.csv'
INTO TABLE `testtable` 
CHARACTER SET 'utf8' FIELDS ESCAPED BY '\\' TERMINATED BY ',' ENCLOSED BY '"' LINES 
TERMINATED BY '\n'
IGNORE 1 LINES 
```

Enter fullscreen mode Exit fullscreen mode

使用反勾号用新生成的数据更新实际的表，以确保保留名称作为字符串被接受。

```
UPDATE testtable tt
inner join actualtable actb on (tt.id = actb.id)
set actb.code = tt.`code`
where tt.id = actb.id; 
```

Enter fullscreen mode Exit fullscreen mode

这产生了期望的结果

```
"id","label","code"
1,"Name","name"
2,"Age","age"
3,"Gender","gender"
4,"Date of birth","date_of_birth" 
```

Enter fullscreen mode Exit fullscreen mode