# 第 17 天-点击 Trello Board - 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-17-one-click-to-trello-board-100-days-100-python-scripts-42en>

## 第 17 天:单击保存

每当我遇到新的框架或技术术语或文章，我都习惯把它们加入我的学习板。如果我觉得有用的话，以后我可以去查阅并开始学习。但我每次都很难打开 trello 并添加它。所以我写了这个剧本。现在，我只需将新术语复制到剪贴板，然后单击可执行格式(或作为快捷方式)的脚本，该术语将存储到 Trello 中，并弹出一个新的通知。

```
#!/usr/bin/python3 from trello import TrelloClient
import pyperclip
import os
import constants

client = TrelloClient(
    api_key=constants.TRELLO_API_KEY,
    api_secret=constants.TRELLO_API_SECRET,
)

all_boards = client.list_boards()

con_list= client.get_board(constants.TRELLO_LEARNING_BOARD_ID).get_lists(list_filter=None)

data=str(pyperclip.paste())

print(con_list[0].add_card(data))

os.system('notify-send "New Card Added" "'+data+'"') 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)