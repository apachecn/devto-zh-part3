# 第 14 天-列出最大的文件- 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-14-list-largest-files-100-days-100-python-scripts-48mn>

## 第十四天:列表 _ 大 _ 文件

这个脚本将打印指定目录及其子目录中 n 个最大的文件。当你想在高清模式下释放一些空间时，它会很有帮助

```
import os,constants
import math
from collections import Counter

LOCATION=constants.DOWNLOAD_PATH
LIST_NUMBER_OF_FILES=25
FILE_SIZE=6250000 #Approx 50 MB 
def convert_size(size_bytes):
   if size_bytes == 0:
       return "0B"
   size_name = ("B", "KB", "MB", "GB", "TB", "PB", "EB", "ZB", "YB")
   i = int(math.floor(math.log(size_bytes, 1024)))
   p = math.pow(1024, i)
   s = round(size_bytes / p, 2)
   return "%s %s" % (s, size_name[i])

files=[val for sublist in [[os.path.join(i[0], j) for j in i[2]] for i in os.walk(LOCATION)] for val in sublist]

files=list(filter(lambda x: os.stat(x).st_size >FILE_SIZE,files))
c = Counter()
for f in files:
    c[f]=os.stat(f).st_size

for item in (sorted(c.items(), key=lambda i: i[1], reverse=True)[:]):
    print(os.path.split(item[0])[1]," - ",convert_size(item[1])) 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)