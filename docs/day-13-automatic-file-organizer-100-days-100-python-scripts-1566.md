# 第 13 天-自动文件管理器- 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-13-automatic-file-organizer-100-days-100-python-scripts-1566>

## 第十三天:文件 _ 组织者

你有没有被下载文件夹里一大堆杂乱无章的文件烦到？如果是，此扩展将帮助您根据文件类型自动组织文件。将它安排为 cron 作业，您永远不必担心手动组织文件夹。如果需要，可以随意添加更多类型。

```
import os,shutil
import constants

DIR_TYPE={
    'Documents':['pdf','docx','doc','csv','txt','xls','xlsx','log'],
    'Archives':['zip','tar'],
    'Pictures':['jpg','jpeg','png','gif'],
    'Code':['py','css','js','html'],
    'Audio':['mp3'],
    "Video":['mp4','srt','mkv','3gp'],
    "Package":["deb",'xip'],
    "Torrent":["torrent"]
}

PATH=constants.DOWNLOAD_PATH

def movefile(file_name,dir_type):
    dir_location=os.path.join(PATH,dir_type)
    if not os.path.exists(dir_location):
        os.mkdir(dir_location)
    shutil.move(file_name,dir_location)

def organize():
    file_types={ file_type:dir_name
        for dir_name,file_types in DIR_TYPE.items() for file_type in file_types
    } # constrcuts a list of filetype:dir_type 
    onlyfiles = [f for f in os.listdir(PATH) if os.path.isfile(os.path.join(PATH, f))] #get all the files 
    for file_name in onlyfiles:
        splited_text=file_name.split('.')
        if len(splited_text)>1:
            ext=splited_text[-1].lower()
            if ext in file_types:
                movefile(os.path.join(PATH,file_name),file_types[ext])
            else:
                print("Unknown File format found -",splited_text[-1])
organize() 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)