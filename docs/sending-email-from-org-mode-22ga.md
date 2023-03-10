# 从组织模式发送电子邮件

> 原文：<https://dev.to/gonsie/sending-email-from-org-mode-22ga>

偶尔，我需要生成一堆电子邮件。通常，我向多组人发送同一封电子邮件，我希望在文本中使用变量，如`$first_name`。虽然我确信有很多在线服务可以帮我实现这一点，但我真的不需要搞一个花哨的活动或使用追踪。另外，我对把别人的邮件提交给第三方持谨慎态度。

当然，我使用过 Emacs，使用 org 模式和 AppleScript。

## 概述

为了完成这项任务，我从一个 org 文件开始。让 AppleScript 从 org 块中执行比我想象的要复杂。相反，我将脚本完整地写在我的 org 文件中，并将其整理到一个单独的`.scpt`文件中。我将数据以表格的形式存储在我的文件中。然后我使用一个 bash 块，它通过`:var`接收数据，循环运行脚本。

## 电子邮件的 AppleScript

我从未正式学习过 AppleScript，但是在 StackOverFlow 和试错法上做一些搜索可以让你走得很远。Mac OS X 在`/Applications/Utilities/Script Editor`中有一个内置编辑器。打开编辑器后，您可以用`File > Open Dictionary...`浏览字典。这本字典列出了不同应用程序的所有类和命令。AppleScript 语言本身看起来像自然语言，相对容易理解…尤其是当别人已经正确地构造了程序。

对于我的电子邮件发送者脚本，我将 AppleScript 源代码存储为一个块。这个块纠结(`C-c C-v C-t`)到一个`.scpt`文件。这个脚本的前半部分设置了一些变量，包括电子邮件的正文。第二部分创建消息并设置“收件人”(也可以设置“抄送”收件人)。

```
#+NAME: script
#+BEGIN_SRC apples :tangle email.scpt
on run argv
   set varName to item 1 of argv
   set varEmail to item 2 of argv
   set varBody to "Hello " & varName & ",

Blah blah blah. Lor ipsum, etc.

Sincerely,
Elsa"

   tell application "Mail"
        set theMessage to make new outgoing message with properties {subject: "Email", content: varBody, visible:true}
        tell theMessage
             make new to recipient with properties {name:varName, address:varEmail}
             #send
        end tell
   end tell
end run
#+END_SRC 
```

注意，我已经注释掉了`send`命令，并设置了消息属性`visible: true`。这意味着草稿电子邮件出现在邮件 GUI 中，但不会自动发送。这允许我在向我的收件人发送垃圾邮件之前进行试运行。

## 填充变量

一旦我写好了脚本和邮件正文，我就知道需要填充什么变量。下一步是将所有数据放入一个命名的 org 表中。我通常手工进行数据格式化，因为我从许多不同的来源收集数据项。

```
#+NAME: data
|    Name    |      Email      |
|------------|-----------------|
| Mx. Person | person@test.com | 
```

由于我纠结于发送电子邮件的 AppleScript 程序，我需要从命令行运行它。我使用 bash 循环和变量数组为数据表中的每一行调用一次脚本。然后，我使用`C-c C-c`运行 bash 循环并生成电子邮件。

```
#+BEGIN_SRC bash :var name=data[,0] email=data[,1]
for i in `seq 0 0`; do
osascript email.scpt "${name[$i]}" "${email[$i]}"
done
#+END_SRC 
```

请注意，我没有计算数据项/行的总数。对于这个简单的例子来说，这需要太多的 bash 魔法。同样，如果你想用一个`echo`语句测试变量，使用`:results output`作为 org 块的参数。

## 点击发送

一旦我对脚本感到满意，我通常会选择手动点击发送每封邮件。

我将所有这些 org 命令保存在一个单独的`notes.org`文件中，这样我就可以记住如何重新创建电子邮件(通常是一年后再次发送它们的时候)。例如，这个[notes.org](https://github.com/womeninhpc/mentoring/blob/master/notes.org)文件为我在 HPC 组织的一个妇女指导项目发送了介绍电子邮件。