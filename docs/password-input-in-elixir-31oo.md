# 在酏剂中输入密码

> 原文：<https://dev.to/tizpuppi/password-input-in-elixir-31oo>

我在寻找一个函数来获得用户输入而不在屏幕上显示，例如从命令行界面获得密码。我在 IO Elixir 库中没有找到任何函数，并且 erlang `:io.password`在运行 Mix 任务时不工作。正如在[这个 Stackoverflow 回答](https://stackoverflow.com/questions/37720961/elixir-or-erlang-prompt-for-password-with-hidden-input)中所建议的，我去看看 hex tasks 是如何实现这个特性的。

# 救援上的流程

这种方法起初看起来有点粗糙，但是非常有效。这个想法是在用户每毫秒输入和清除一行时产生一个进程。在用户键入之后，通过发送适当的消息来停止该过程。

在这里你可以看到完整的代码片段，从十六进制库[中提取。](https://github.com/hexpm/hex/issues/2) 

```
defmodule Get do
  # Password prompt that hides input by every 1ms
  # clearing the line with stderr
  def password(prompt) do
    pid = spawn_link(fn -> loop(prompt) end)
    ref = make_ref()
    value = IO.gets("#{prompt} ")

    send(pid, {:done, self(), ref})
    receive do: ({:done, ^pid, ^ref} -> :ok)

    value
  end

  defp loop(prompt) do
    receive do
      {:done, parent, ref} ->
        send(parent, {:done, self(), ref})
        IO.write(:standard_error, "\e[2K\r")
    after
      1 ->
        IO.write(:standard_error, "\e[2K\r#{prompt} ")
        loop(prompt)
    end
  end
end 
```

首先产生一个进程，新创建的进程进入一个循环。每隔一毫秒，屏幕上会显示以下字符串:" \e[2K\r "。这是一个 [XTerm 控制序列](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html)，它擦除所有行(ESC[2K]，然后将光标带回行的开头(\r)。

从命令行读取值后，会向衍生的进程发送一条消息，通知输入结束。然后，该过程结束循环，并向父进程发信号告知他已被关闭。

我认为在这段代码中有一些有趣的提示:

*   通过在`:standard_error`上而不是在`:standard_input`上写入来清除行。我认为这是因为`:standard_input`阻止接收用户输入的内容
*   在父进程和子进程之间交换一些消息:特别是父进程等待子进程确认他已经完成。这可能是为了防止最后一个字符没有被清除的情况(在`:done`块中的最后一个`IO.write`)和子进程清除下一个提示行的情况(在父进程中的接收块)。
*   `make_ref`用于创建唯一引用，并在发送消息时用作“唯一性”标志。