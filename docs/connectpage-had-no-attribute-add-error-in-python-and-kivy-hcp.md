# Python 和 Kivy 中的“ConnectPage”没有属性“add”错误

> 原文：<https://dev.to/yahucoder/connectpage-had-no-attribute-add-error-in-python-and-kivy-hcp>

亲爱的大家，
日安，当我尝试运行这个程序时，得到的错误消息是
//‘connect page’没有属性‘add’

我的代码可以在下面找到

import kivy
import OS
OS .大约[' kivy _ GL _ 后端'] = 'angle_sdl2 '

from kivy import Config
Config . set(' graphics '，' multisamples '，' 0 ')

从 kivy.app 导入 App
从 kivy.uix.label 导入标签
从 kivy.uix.gridlayout 导入 GridLayout
从 kivy.uix.textinput 导入 TextInput
kivy . require(" 1 . 10 . 1 ")

类 connect page(GridLayout):
def**init**(self， **kwargs):
super()。**init**(**kwargs)
self . cols = 2
self . add . widget(Label(text = " Username "))
self。username = TextInput(multiline = False)
self . add _ widget(self。用户名)

```
 self.add.widget(Label(text="Password"))
    self.Password = TextInput(multiline=False)
    self.add_widget(self.Password) 
```

类 adu 360(App):
def build(self):
return connect page()

如**名** == " **主** ":
ADU360()。运行()

我真的很感谢你们的帮助。提前感谢。