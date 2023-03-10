# 用你的方式来反应本地开发菜单

> 原文：<https://dev.to/hrastnik/keybind-your-way-to-react-native-dev-menu-1p6l>

*本教程仅适用于安卓手机*

如果你是一个 React 本地开发者，你应该已经知道通过摇动手机来打开 dev 菜单是非常令人沮丧的。这个问题已经被一些人解决了，解决方案以组件的形式存在，这些组件包装了你的整个应用程序，使 3 个手指长按能够访问开发菜单。这是可行的，但就我个人而言，我不喜欢在我的应用程序中有开发专用组件的想法，所以它从未困扰过我。

这就是为什么我创建了这个小的解决方法，使用键盘快捷键从 VSCode 内部打开 dev 菜单。唯一的警告是，你的手机必须通过 USB 电缆连接，但由于这具有加载速度更快的优势，我已经习惯了连接，所以快捷方式是一个明显的改进。下面是怎么做的。

首先，我们将在 VSCode 中创建一个运行两个 ADB 命令的任务。第一个命令会将电话流量重定向到我们的计算机，并加快加载速度。当您运行`react-native run-android`时，这个命令和其他命令一样，是在后台运行的。
第二个命令发送一个将弹出菜单的按键事件。
从技术上讲，我们不需要第一个命令，但是如果你把你的手机从电脑上断开，然后重新连接，你就必须运行它来获取 JS 代码的变化，所以拥有它是很好的。

因此，让我们来设置 VSCode 任务。打开 VSCode，按 CTRL+P，然后输入“任务:配置任务”。按 enter 键并选择“从模板创建 tasks.json 文件”，从可用选项中选择“其他”。现在，您应该会看到一个简单的模板，用于通过任务运行 shell 命令。将其更改为如下所示:

```
 {
    "version": "2.0.0",
    "tasks": [
      {
        "label": "reactNativeDevMenu",
        "type": "shell",
        "presentation": {
          "echo": false,
          "reveal": "never",
          "focus": false,
          "panel": "new",
          "showReuseMessage": false,
          "clear": false
        },
        "command": "adb reverse tcp:8081 tcp:8081 && adb shell input keyevent 82"
      }
    ]
  } 
```

label 键充当一个 ID，我们稍后可以使用它来运行任务。type 键告诉 VSCode 任务应该在 shell 中运行。
需要 presentation 键，以防止在我们运行任务后终端弹出。这样它在后台安静地运行。
命令键是我们运行任务后将运行的实际命令。

现在让我们绑定一个键盘快捷键来运行任务。我使用了 CTRL+ALT+D，但是你可以使用任何你想要的。将这个代码片段添加到 keybinding.json 文件中。您可以通过按 CTRL+SHIFT+P 键输入“首选项:打开键盘快捷键”，然后按右上方的花括号来访问该文件。

```
 {
    "key": "ctrl+alt+d",
    "command": "workbench.action.tasks.runTask",
    "args": "reactNativeDevMenu"
  } 
```

args 下的值应该与我们在 tasks.json 中设置的标签相匹配。

现在用 USB 线连接你的安卓手机，打开你的应用程序，试试你设置的按键绑定。恭喜你！你现在可以打开开发菜单，而不用像个疯子一样挥舞着手机。黑客快乐！