# 第 6 天和第 7 天- 100 天 100 个 Python 脚本挑战

> 原文：<https://dev.to/ganesh/100-days-100-python-scripts-challenge-5172>

## 第六天:convert_pdf_to_csv

这个脚本帮助你将给定的 PDF 文件转换成 CSV 文件并存储在本地

```
 from tabula import read_pdf
import pandas as pd

FILE_NAME="" #Ppdf file as input dest="" #destination csv file name 
df= read_pdf(FILE_NAME)
x=df.values
data=pd.DataFrame(data=x[1:,1:],columns=x[0,1:])

data.to_csv(dest,sep=',',encoding="utf-8") 
```

Enter fullscreen mode Exit fullscreen mode

## 第七天:推送 _ 今日 _ 脚本

这个脚本将自动跟踪更新的文件，提交这些文件，并将它们推送到 git。

```
import os,subprocess
from constants import WORKING_DIR

os.chdir(WORKING_DIR)

cmd = "git add ."
subprocess.check_output(cmd, shell=True)  

status= "git status -s"
returned_value = subprocess.check_output(status, shell=True) 

print('updated files:', returned_value)

if not returned_value==b'':
    commit_message='"Done Changed in the following files '
    if len(str(returned_value).split(r'\n'))>0:        
        for file_name in str(returned_value).split(r'\n'):
                    commit_message=commit_message+file_name[5:]+" "
        cmd= "git commit -m "+commit_message+r'"'
        print("Commit message ",subprocess.check_output(cmd, shell=True))
        try:
            cmd= "git push origin master"
            result = subprocess.check_output(cmd, shell=True)
            print(result) 
        except:
            cmd = "git pull"
            returned_value = subprocess.check_output(cmd, shell=True)  
            cmd = "git push origin master"
            result = subprocess.check_output(cmd, shell=True)
            print(result) 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)