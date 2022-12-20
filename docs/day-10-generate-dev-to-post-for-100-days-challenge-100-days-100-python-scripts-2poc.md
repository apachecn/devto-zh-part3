# 第 10 天-生成开发发布 100 天挑战- 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-10-generate-dev-to-post-for-100-days-challenge-100-days-100-python-scripts-2poc>

## 第 10 天:开发 _ 到 _ 发布 _ 生成

这个脚本使用一个模板，通过检查 Readme 中最近更新的文件，为 dev.to 生成我的 100 天 python 脚本帖子。这有助于我快速更新 dev.to post，而不用一次又一次手动复制粘贴东西

```
import constants,os
import pyperclip
with open('README.md') as f:
    data=f.readlines()
last_updated_date=None

desc=""
for c in reversed(data):
    if "## Day " in c:
        last_updated_date=c.strip()
        break
    desc=c+desc

day,file_name=last_updated_date=last_updated_date[3:].split(":")

user_title=input("Please enter title for "+file_name.strip()+"\n")

title=day.title()+" - "+user_title
desc=desc[2:]

print(desc)
with open(os.path.join(constants.WORKING_DIR,file_name.strip())) as f:
    code= f.read()

with open("data/devto_post_template") as f:
    template=f.read()

template=template.replace("###code###", code)
template=template.replace("This script uses a template and generates my 100 days python scripts posts for dev.to by checking the last updated file in Readme. This is helpful for me to quickly update dev.to post without manually copy pasting things again and again", desc)
template=template.replace("Day 10 - Generate Dev.to post for 100 days challenge", title)
template=template.replace("Day 10", day.title())
template=template.replace("dev_to_post_generate", file_name.strip().replace(".py", ""))

pyperclip.copy(template) 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)