# 如何使用 Fusuma 在 Linux 上定制触摸板手势

> 原文：<https://dev.to/iberianpig/how-to-install-and-customize-fusuma-73l>

我向 RubyGems 发布了“Fusuma ”,它可以识别 Linux 触摸板上的滑动或挤压手势。
[https://github.com/iberianpig/fusuma](https://github.com/iberianpig/fusuma)

您可以添加触摸板手势来运行命令或常见任务，如更改工作区、在浏览器上后退或前进等。

这是我的第一篇文章，是如何使用和定制 Fusuma。

# 安装扶桑

如果你使用基于 Debian 的 OS(使用 apt 进行包管理)，你可以像“apt install”一样直接使用下面的命令。

我在戴尔 XPS 13(9360)的基本操作系统 Loki 上使用 fusuma。

对于 Ubuntu 15.04 及更高版本，可以使用依赖于 Fusuma 的 libinput，但对于旧版本的 Ubuntu，应该尝试使用 xswipe 来使用 Synaptics 驱动程序。

## 确认安装的 Ruby 版本

请首先确认您是否安装了 ruby。

你可以在系统范围内使用 fusuma 和 Ruby it。

(当然就算是 rbenv 和 rvm 也可以)

```
$ ruby -v

ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-linux]
# confirm the version of ruby installed 
```

Enter fullscreen mode Exit fullscreen mode

## 安装依赖包

安装`libinput-tools`用于读取触摸板输入。

(Fusuma 自身使用`libinput debug-events`的调试日志输出)

```
$ sudo apt install libinput-tools 
```

Enter fullscreen mode Exit fullscreen mode

还要安装`xdotool`，以便扶桑触发快捷键，如`Alt + Left`、`Alt + Right`等。

```
$ sudo apt install xdotool 
```

Enter fullscreen mode Exit fullscreen mode

## 从 RubyGems 安装 Fusuma

```
$ sudo gem i fusuma 
```

Enter fullscreen mode Exit fullscreen mode

`i`是`install`的缩写。如果你想在系统范围内安装 fusuma 到 Ruby 就需要`sudo`。

(如果使用已经通过 rbenv 和 rvm 安装的 Ruby，可以用`$ gem install fusuma`安装)

## 为 Fusuma 创建配置文件

创建一个配置文件到`~/.config/fusuma/config.yml`

```
$ mkdir -p ~/.config/fusuma
$ touch ~/.config/fusuma/config.yml 
```

Enter fullscreen mode Exit fullscreen mode

在编辑器中打开`~/.config/fusuma/config.yml`，然后粘贴以下内容。

```
swipe:
  3:
    left:
      command: 'xdotool  key  alt+Right'
    right:
      command: 'xdotool  key  alt+Left'
    up:
      command: 'xdotool  key  ctrl+t'
    down:
      command: 'xdotool  key  ctrl+w' 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子是通过三个手指的滑动注册浏览器的快捷方式。
(后退/前进/创建标签/关闭标签)

## 将用户添加到输入组

要执行 Fusuma，它需要向用户授予读取触摸板输入的权限。

```
$ sudo gpasswd -a $USER input 
```

Enter fullscreen mode Exit fullscreen mode

`gpasswd`命令将用户添加到输入组。
( `$ USER`是运行时的用户名)

**需要从 X** 注销并登录(或重启)

没有这个过程，Fusuma 无法识别触摸板。
因为它必须在输入组中反映添加的用户。

# 发射扶桑

打开终端，键入 Fusuma 和 endter。

```
$ fusuma 
```

Enter fullscreen mode Exit fullscreen mode

当你用三个手指在浏览器上滑动时，如果所有的动作(后退/前进/创建标签页/关闭标签页)都正常，那就没问题。

## 终端关闭时保持后台运行

```
$ fusuma -d 
```

Enter fullscreen mode Exit fullscreen mode

这个`-d`选项作为 Unix demon 运行，它与终端进程断开连接。
因此`fusuma`在关闭终端后仍然可用。

# 配置要分配手势的命令

在`~/.config/fusuma/config.yml`中添加识别手势并向其分配命令。

## 在终端确认命令运行

要切换工作区，您可以设置`xdotool key ctrl + alt + Up`或`xdotool key ctrl + alt + Down`
我建议您尝试这样的命令在终端上正常工作。

在本例中，工作区的快捷方式取决于 WindowManager。(Gnome 3 用`xdotool key ctrl + alt + Up`和`xdotool key ctrl + alt + Down`上下移动)

## 在四指轻扫中切换工作区

将以下内容粘贴在`swipe`部分
的底部

```
 4:
    up: 
      command: 'xdotool  key  ctrl+alt+Down'
    down: 
      command: 'xdotool  key  ctrl+alt+Up' 
```

Enter fullscreen mode Exit fullscreen mode

如果重写配置文件，必须在`Ctrl-c`或`kill`命令停止后重新运行`fusuma`命令。

如果能确认自定义四指滑动的操作，还是可以的。

# Fusuma 的自动启动设置

将 fusuma 设置为自动启动，因为每次重启笔记本电脑时打开终端并执行 Fusuma 命令很麻烦。

1.  在终端中输入`which fusuma`，记下启动 Fusuma 的路径

2.  启动`gnome-session-properties`，通过在末尾添加`-d`选项(daemonize)输入上一步记录的启动路径。

# 更新扶桑

```
$ sudo gem update fusuma 
```

Enter fullscreen mode Exit fullscreen mode

(如果已经在 rbenv 或 rvm 上安装了带 Ruby 的 fusuma，可以用`$ gem update fusuma`更新)

# 其他定制

在`~/.config/fusuma/config.yml`中，您可以设置缩放(`pinch in` / `pinch out`)和每个手势的灵敏度(`threshold`)。
也可以设置连续收缩的手势。

更多信息见[扶桑库自述文件](https://github.com/iberianpig/fusuma/blob/master/README.md)。

# 发现 bug 时

如果您发现任何 bug 或问题，请将问题注册到 [Github 问题](https://github.com/iberianpig/fusuma/issues)。

# 捐赠

要不要支持扶桑？查看完全可选的捐赠选项，这是一种直接支持扶桑正在进行的发展的方式:[https://www.patreon.com/iberianpig](https://www.patreon.com/iberianpig)