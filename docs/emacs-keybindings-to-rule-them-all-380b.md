# Emacs 键绑定来管理它们

> 原文：<https://dev.to/justintanner/emacs-keybindings-to-rule-them-all-380b>

当微软发布适用于 Windows 10 的 Ubuntu 时，我抓住了这个机会，在苹果电脑上运行了 8 年之后，尝试在 Windows 上进行网络开发。我对 Windows 10 上的 Ubuntu 感到惊喜。在你的终端上安装 Ubuntu 很方便，因为它更类似于我部署代码的服务器。但是切换回 Windows 有一个令人沮丧的方面，键盘快捷键。

有些差异很容易解决，例如`Ctrl+c`和`Cmd+c`(复制)。复制和粘贴可以通过改变操作系统中的设置来处理，但其他快捷方式则完全不同，如`Cmd+q`和`Alt+F4`(退出应用程序)。我想知道是否有更好的解决方案，而不是记住这些差异。

我选择的文本编辑器是 Emacs。一旦进入 Emacs，你就进入了另一个键盘快捷键的世界。再见`Ctrl+c`和你好`Ctrl+y`。导航文本从按住修饰键开始，如`Ctrl`或`Alt`，你的手不会离开主行。有可能使用 Emacs 快捷方式以同样的方式控制两个操作系统吗？

## 有没有已经这样做的 app？

我开始寻找可以下载或购买的东西来解决问题。对于 windows，我找到了 XKeymacs，它将自己描述为“一个键盘工具，可以在所有 Windows 应用程序上实现 Emacs 一样的可用性”。

在 Mac 上，我找不到任何解决这个问题的应用程序，但 OSX 支持**大多数** Emacs 的开箱即用快捷方式。例如`Ctrl+a`被映射到行字符的开始，就像 Emacs 一样。

XKeymacs 和 Mac 的默认快捷键都不支持两个重要的 Emacs 特性:

1.  用`Ctrl+Space`(开始标记)选择文本，向下移动几行以扩展文本选择和`Ctrl+Space`(结束标记)
2.  前缀键或和弦快捷键涉及一个修饰键和**两个**其他键如`Ctrl+xs`(保存)

## 有没有这样做的脚本？

接下来我在 Github 上搜索解决方案，发现了一些有趣的、[脚本](https://github.com/usi3/emacs.ahk)。这些脚本没有解决选择文本或和弦键的问题，但它们是在两个对我来说是新的应用程序/框架中编写的。

*   一种基于 Lua 的脚本语言，用于拦截击键和鼠标事件(Mac)
*   自动热键(AutoHotkey)拦截击键和鼠标事件的脚本语言(Windows)

Hammerspoon 和 AutoHotkey 有潜力，但我需要确保它们能做我想做的事情，所以我在这些新环境中编写了一些实验代码。

## 自动热键中的文本选择

首先，我想看看 AutoHotkey 能否[模仿 Emacs 风格的文本选择](https://gist.github.com/justintanner/272aa3a9b5834a1e4027aaaa3702efbe)。

```
#SingleInstance
#Installkeybdhook
#UseHook
SetKeyDelay 0

global selecting := False

SendWithShift(keys)
{
  If (selecting)
  {
    Send +%keys%
  }
  Else
  {
    Send %keys%
  }
}

^Space::
selecting := !selecting
Return

^n::
SendWithShift("{Down}")
Return

^p::
SendWithShift("{Up}")
Return

^f::
SendWithShift("{Right}")
Return

^b::
SendWithShift("{Left}")
Return

^a::
SendWithShift("{Home}")
Return

^e::
SendWithShift("{End}")
Return 
```

Enter fullscreen mode Exit fullscreen mode

## 槌勺中的和弦

接下来我检查了 Hammerspoon 是否能够执行和弦键。

```
local ctrlXActive = false
local hotkeyModal = hs.hotkey.modal.new()

function startCtrlX()
  ctrlXActive = true

  hs.timer.doAfter(1, clearCtrlX)
end

function clearCtrlX()
  print('Clearing ctrlXActive flag.')
  ctrlXActive = false
end

function forwardOrOpen()
  hotkeyModal:exit()

  if ctrlXActive then
    print('Opening file.')
    hs.eventtap.keyStroke('cmd', 'o')
  else
    print('Moving cursor forward.')
    hs.eventtap.keyStroke(nil, 'right')
  end

  hotkeyModal:enter()

  clearCtrlX()
end

hotkeyModal:bind({'ctrl'}, 'x', startCtrlX, nil, nil)
hotkeyModal:bind({'ctrl'}, 'f', forwardOrOpen, nil, nil)
hotkeyModal:enter() 
```

Enter fullscreen mode Exit fullscreen mode

## 我需要数据结构

成功！实验向我展示了 Hammerspoon 和 AutoHotkey 可以以我想要的方式模拟 Emacs。接下来，我需要一个数据结构来存储翻译击键所需的所有信息。

### Lua 中的表格

Hammerspoon 是用类似于 Ruby 的动态语言 Lua 编写的。在 Ruby 中关联数组被称为哈希，在 Lua 中它们被称为[表](https://www.lua.org/pil/2.5.html)。

```
user = {}
user['name'] = 'Yuval'
user['job'] = 'Author' 
```

Enter fullscreen mode Exit fullscreen mode

下面是 Ruby 中的一个等价散列:

```
user = {}
user[:name] = 'Yuval'
user[:job] = 'Author' 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，Lua 没有使用 JSON 风格的语法来定义多个属性，比如:

```
user = { name: 'Yuval', job: 'Author' } 
```

Enter fullscreen mode Exit fullscreen mode

在 Lua 中看起来应该是这样的:

```
user = { ['name'] = 'Yuval', ['job'] = 'Author' }

or

user = { name = 'Yuval', job = 'Author' } 
```

Enter fullscreen mode Exit fullscreen mode

Lua 也使用表来创建数组:

```
fruits = { 'Apple', 'Banana', 'Orange' }

print(fruits[1])  -- Prints 'Apple' because Lua indexes arrays by 1 not 0. 
```

Enter fullscreen mode Exit fullscreen mode

在 Ruby 中可能是:

```
fruits = ['Apple', 'Banana', 'Orange']

puts fruits[0] 
```

Enter fullscreen mode Exit fullscreen mode

表格似乎是我需要的数据结构，用来存储我想要翻译的击键。

### 自动热键中的对象

在 AuotHotKey 中，关联数组称为[对象](https://autohotkey.com/docs/Objects.htm)。

```
User := {}
User["name"] := "Yuval"
User["job"] := "Author" 
```

Enter fullscreen mode Exit fullscreen mode

AHK 也可以使用类似 JSON 的语法:

```
User := { name: 'Yuval', job: 'Author' } 
```

Enter fullscreen mode Exit fullscreen mode

类似于 Lua，AHK 使用对象来定义关联数组和常规数组:

```
Fruits := ['Apple', 'Banana', 'Orange']

OutputDebug %Fruits[1]% ; Prints 'Apple' because AHK also uses 1-based array indexes 
```

Enter fullscreen mode Exit fullscreen mode

## 按键翻译数据结构

为了存储击键的查找表，我从一个 Lua 表开始，如下所示:

```
local keys = {
  ['ctrl'] = {
    ...
    ['b'] = {nil, 'left'},
    ['w'] = {'cmd', 'x'},
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

点击`Ctrl+b`将触发左方向键击键，这不需要按住修饰键如`Ctrl`，点击`Ctrl+w`将触发 Mac 剪切快捷方式`Cmd+x`。

这个配置将修饰符和普通键分开到不同的数组条目中，因为这是 Hammerspoon 的 API 激活键的方式。

```
 hs.eventtap.event.newKeyEvent(mods, key, true):post()
  hs.eventtap.event.newKeyEvent(mods, key, false):post() 
```

Enter fullscreen mode Exit fullscreen mode

在 AutoHotkey 中，修饰键和常规击键可以混合使用。下面是 AHK 相同的数据结构:

```
global keys
:= : {"ctrl"
    : {"b": ["{Left}"]
      ,"w": ["^x", False, ""]}} 
```

Enter fullscreen mode Exit fullscreen mode

`Ctrl+w`触发窗口剪切快捷方式`Ctrl+x`，也就是 AHK 的`^x`。该语法遵循 AHK 的[多行延续](https://autohotkey.com/docs/Scripts.htm#continuation)规则，确保每一行都以一个字符开始，该字符表示该数据结构将跨越多行。

## 分隔文本选择键

在 Emacs 中使用`Ctrl+Space`开始文本选择后，您可以使用`Ctrl+n`(向下)等导航快捷方式在文档中导航，以增大或减小选择的大小。相比之下，其他快捷键在文本被选中时无法工作，例如`Ctrl+k`(删除一行文本)。为了支持文本选择，我的配置数据需要区分这两种类型的快捷方式。

```
local keys = {
  ['ctrl'] = {
    ...
    ['b'] = {nil, 'left', true},
    ['d'] = {'ctrl', 'd', false},
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Ctrl+b`通过向后移动一个字符来缩小当前文本选择，因此它被标记为`true`作为第三个配置设置。`Ctrl+d`在当前光标位置删除一个字符，该字符需要先取消选择当前文本选择，所以标记为`false`作为第三个配置设置。

## 用宏解决限制

数据结构的最后一个附加项是执行宏而不是击键的选项。这里有一个 AHK 写的宏:

```
,"k": ["", False, "MacroKillLine"]
...

; Macro to kill a line and add it to the clipboard
MacroKillLine()
{
  Send {HOME}{ShiftDown}{END}{ShiftUp}
  Send ^x
  Send {Del}
} 
```

Enter fullscreen mode Exit fullscreen mode

这个宏试图模拟 Emacs 删除行的方式，首先选择整行，剪切它，然后删除它。

## 在和弦上妥协

当我在研究默认的 Emacs 键绑定时，我注意到 Emacs 中一些我从未想过的快捷方式。

文件 File 被绑定到:`Ctrl-x Ctrl-f`(在键入 x 和 f 之间释放 Ctrl)

和

设置填充列绑定到:`Ctrl-x f`(在键入 x 和 f 之间按住 Ctrl)

这些快捷键执行不同的动作，但是利用了**放开 Ctrl** 和**按住 Ctrl** 的细微差别。我应该在脚本中支持这些快捷方式吗？在查看了少量在这方面有所不同的 Emacs 快捷方式后，我决定在脚本中放弃对它们的支持，因为我反正不会使用它们。我可能是在 Emacs 中不小心输入了这些快捷方式。我决定用一个 [elisp 配置脚本](https://gist.github.com/justintanner/05790ad55325408ff70632ca4ddd9f5f)禁用 Emacs 中的这些快捷方式。

## 任务切换

与编辑文本无关的一个重要快捷键是`Alt+Tab`或`Cmd+Tab`(任务切换器)。随着我的剧本完成，我注意到`Alt+Tab`是我的手离开主场的唯一原因。我可以将这些快捷方式重新绑定到一些未分配的 Emacs 快捷方式，并保持我的手在 home-row 上吗？

使用`Ctrl`从 A 到 Z 的所有快捷键都已被使用。像`Ctrl-xt`这种未分配的和弦键呢？

我勾上了`Ctrl+xt`快捷方式，立刻命中一道题。在 Windows 上`Alt+Tab`需要按住`Alt`键并点击`Tab`键来浏览每个当前运行的应用程序，但我想点击`Ctrl-xt`然后`Tab`来浏览当前运行的应用程序。我的脚本中处理和弦的代码不支持这种快捷方式。

在 stackoverflow 上，我发现其他程序员在 [AHK](https://stackoverflow.com/questions/35971452/what-is-the-right-way-to-send-alt-tab-in-ahk) 中也遇到了同样的问题。Windows 上的一个解决方案是使用`Alt+Esc`而不是`Alt+Tab`。`Alt+Esc`切换到在没有用户界面的情况下打开的**最后一个应用**。这与我的脚本兼容，但实际上我发现用这种快捷方式切换应用程序令人困惑。尝试编写《T3》和《T4》的剧本似乎是个死胡同。

作为最后的手段，我寻找了内置于每个操作系统的替代任务转换器。对于 Windows，我找到了一个免费的开源项目 [switcheroo](http://www.switcheroo.io/) 。我将`Ctrl+xt`设置为启动 switcheroo 而不是`Alt+tab`，一切都很好。在 Mac 上，我采取了同样的方法，购买了一款名为[女巫 3](https://manytricks.com/witch/) 的付费应用。

## 结果

剧本成功了！我每天都在 Windows 和 Mac 上使用它们。操作系统之间的切换不再是一种痛苦。

如果你想亲自给他们一次机会，请查看 Github 库和快乐脚本。