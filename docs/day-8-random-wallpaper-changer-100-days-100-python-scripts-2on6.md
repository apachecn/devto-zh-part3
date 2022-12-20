# 第 8 天-随机更换壁纸- 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-8-random-wallpaper-changer-100-days-100-python-scripts-2on6>

## 第八天:更新 _ gnome _ 壁纸

这个脚本将从一个文件夹中随机选择一个图像文件，并更新 ubuntu
中的壁纸

```
import os,random
import constants

WALLPAPER_DIR= constants.WALLPAPER_DIR # Wallpaper Location 
images=[item for item in os.listdir(WALLPAPER_DIR) if os.path.isfile(os.path.join(WALLPAPER_DIR,item)) and item.split(".")[-1].lower() in ["png",'jpg']]

if len(images) >0:
    wallpaper=random.choice(images)
    os.system("gsettings set org.gnome.desktop.background picture-uri file://"+os.path.join(WALLPAPER_DIR,wallpaper)) 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)