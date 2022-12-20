# 第 5 天- 100 天 100 个 python 脚本挑战

> 原文：<https://dev.to/ganesh/day-5-100-days-100-python-scripts-challenge-14hg>

要了解全部挑战，请点击此链接。

[点击此处阅读 100 天 100 个 Python 脚本挑战](https://dev.to/ganesh/100-days-100-python-scripts-challenge-3fc)

## 第五天:追加 _ 阅读 _ 我

所以每天当我创建一个新的脚本时，我必须手动更新 read.me 文件。但这将自动读取之前的日期和最后更新的文件，并将其附加到自述文件中。一旦我给出描述，它也会用适当的空格更新它。

```
import glob
import os,sys

pwd=os.getcwd()+r"/"

list_of_files = glob.glob(pwd+'*.py') 
latest_file = max(list_of_files, key=os.path.getctime)

last_updated_python_file_name=latest_file.split(r"/")[-1]

data=[]
with open('README.md') as f:
    data=f.readlines()
last_date=None
is_already_added=False
for c in reversed(data):
    if "## Day " in c:
        if not last_date:
            last_date=c.strip()
        if last_updated_python_file_name in c:
            is_already_added=True
            break
if is_already_added:
    print("Last updated file "+last_updated_python_file_name+" is already added")
    sys.exit(0)

try:
    today_number=int(last_date[7])+1
except:
    pass

new_data="\n\n## Day "+str(today_number)+": "+last_updated_python_file_name+"\n"

desc=input("Please Enter description for "+last_updated_python_file_name+"\n")

new_data=new_data+desc.strip()

with open('README.md',"a+") as file:
    file.write(new_data) 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)