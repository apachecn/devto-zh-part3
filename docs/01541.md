# 使用熊猫阅读 Excel 表格

> 原文：<https://dev.to/mu/read-excel-sheet-using-pandas-300h>

```
import pandas as pd

EXCEL_FILE = 'source.xlsx'

df= pd.read_excel(open(EXCEL_FILE, 'rb'),skiprows=3,index=False)

print(df) 
```

Enter fullscreen mode Exit fullscreen mode