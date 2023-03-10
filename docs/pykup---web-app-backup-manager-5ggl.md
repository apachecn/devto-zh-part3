# PyKup - Web 应用程序备份管理器

> 原文：<https://dev.to/robyferro/pykup---web-app-backup-manager-5ggl>

[![Pykup](img/4fbfb34d7eb6d3765c7fec34c1eda836.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--doTgDcOn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/RobyFerro/PyKup/master/logo.jpg)

# PyKup - WebApp 备份管理器

一个简单的工具，可以轻松管理和安排任何 web 应用程序的备份！使用 PyKup，您只需一个命令就可以备份您的 web 应用程序。
所有东西都会被压缩，存放在你最喜欢的存储位置。

## 用法

```
python pykup.py -d <APP_FOLDER> -cF <PATH_CONFIG_FILE> -uD <UPLOAD_DRIVER dropbox|scp> --cron 
```

Enter fullscreen mode Exit fullscreen mode

## 多个应用程序备份

借助 crontab 集成，您可以安排多个应用程序备份:

```
python pykup.py -d <APP_FOLDER_1> -cF <PATH_CONFIG_FILE_1> -uD <UPLOAD_DRIVER dropbox|scp> --cron 
python pykup.py -d <APP_FOLDER_2> -cF <PATH_CONFIG_FILE_2> -uD <UPLOAD_DRIVER dropbox|scp> --cron 
```

Enter fullscreen mode Exit fullscreen mode

## 入门

首先确保你的机器上安装了 Python 3，然后你可以用:
获取所有文件

```
git clone https://github.com/RobyFerro/PyKup.git 
```

Enter fullscreen mode Exit fullscreen mode

用
安装所有需求

```
pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

之后，您可以通过键入:
获得所有信息

```
usage: pykup.py [-h] -d DIRECTORY [-n APP_NAME] -cF CONFIG_FILE
                [-uD UPLOAD_DRIVER] [-rF REMOTE_FOLDER] [--cron]

PyBack WebApp backup utils

optional arguments:
  -h, --help         show this help message and exit
  -d DIRECTORY       Set a backup directory
  -n APP_NAME        Define application name
  -cF CONFIG_FILE    Define configuration file
  -uD UPLOAD_DRIVER  Define upload driver dropbox|scp
  -rF REMOTE_FOLDER  Define scp remote folder
  --cron             Set command in crontab
  --telegram         Send telegram notification after backup 
```

Enter fullscreen mode Exit fullscreen mode

## 配置

要配置您的 web 应用程序，您应该在配置目录
中重命名 config.ini.example，并用您的参数替换所有值。

您可以设置多个。ini 文件来安排多个备份

```
first-app.ini
second-app.ini
third-app.ini 
```

Enter fullscreen mode Exit fullscreen mode

这个技巧与- cron 选项结合使用会很有用。

## Crontab 事件

要在 linux crontab 作业中设置当前命令，您只需指定- cron 选项。
使用以下命令显示所有现有的 crontab 作业:

```
crontab -l 
```

Enter fullscreen mode Exit fullscreen mode

## Dropbox 集成

要在 dropbox 上上传您的备份，您必须在您的 [Dropbox 帐户](https://www.dropbox.com/developers/apps/create)
中创建一个新的应用程序，并遵循以下步骤:

*   选择 Dropbox API
*   选择“应用程序文件夹”
*   选择您的应用名称

创建之后，您必须生成一个新的访问令牌，并将其放在 config/integrations/Dropbox . JSON 上

## 电报确认通知

要向您自己的电报频道发送通知，您必须创建一个[电报机器人](https://core.telegram.org/bots#6-botfather)

*   在 config.ini 文件中插入您的令牌和电报用户 ID

### 全部清单:

*   [x]文件备份和压缩
*   [x]记录活动
*   [x] PostgreSQL 转储
*   [x] MySQL 转储
*   [x] Dropbox 集成
*   [x] SCP Sync
*   [x] Rsync 集成
*   [x] Crontab 集成
*   [x]电报确认通知
*   [ ]电报机器人远程控制
*   [ ] Google Drive 集成

## 需要帮助？

加入我们的[电报社区](https://t.me/ikdev)