# 使用 Python Pandas 从 SQLITE 读取数据/向 SQLITE 写入数据

> 原文：<https://dev.to/mu/read-write-data-from-to-sqlite-using-python-pandas-11mg>

# 将 EXCEL 文件推送到 SQLITE 文件

```
import pandas as pd
import sqlite3

EXCEL_FILE_NAME = 'source.xlsx'
DB_NAME = 'stage.db'

df= pd.read_excel(open(EXCEL_FILE_NAME , 'rb'),skiprows=3,index=False)

conn = sqlite3.connect(DB_NAME)
df.to_sql('source', conn) 
```

Enter fullscreen mode Exit fullscreen mode

# 从 SQLITE 中读取

```
 import pandas as pd
import sqlite3

DB_NAME = 'stage.db'

conn = sqlite3.connect(DB_NAME)
sql_string = 'select * from source_tbl where id>10'
df = pd.read_sql(sql_string, conn)
print(df) 
```

Enter fullscreen mode Exit fullscreen mode