# 消毒狂欢历史

> 原文：<https://dev.to/lbonanomi/sterilizing-bash-history-5455>

对于许多 Linux 系统操作工程师来说，共享账户是一个令人不快的事实。当共享帐户的在家感觉使你忘记你没有登录到自己的 shell，并且你不小心使用内嵌密码进行 curl 调用或使用带有`--password`开关的命令时，这可能是一个问题。让我们利用一个模糊的 bash 环境变量和一点 go 来编辑我们共享的 shell 历史中的凭证。

我们需要一个工具来识别接受内联密码或令牌的命令，并用占位符文本替换敏感字段。为了提高执行速度，我们将尝试使用 go 语言生成一个编译后的二进制文件，这样我们的 shell 就不会陷入太多的困境。

```
package main

import (
    "bufio"
    "fmt"
    "os"
    "regexp"
    "strings"
)

func curl_u(cmdline string)(after string) {
    curl := regexp.MustCompile(`:\w+?\S+\b`)
    after = curl.ReplaceAllString(cmdline, ":REDACTED ")
    return
}

func https_creds(cmdline string)(after string) {
    pattern := regexp.MustCompile(`https://(\S+?):\S+?@`)
    after = pattern.ReplaceAllString(cmdline, "https://REDACTED:REDACTED@")
    return
}

func header_creds(cmdline string)(after string) {
    pattern := regexp.MustCompile(`(-H|--header)\s.*?(token|auth.*?)\s\S+?\s`)
    after = pattern.ReplaceAllString(cmdline, "-H AUTH_HEADER_REDACTED ")
    return
}

func main() {
    reader := bufio.NewReader(os.Stdin)

    for {
        text, _ := reader.ReadString('\n')

        if (text == "") {
            break
        }

        newtext := ""

        for _, word := range(strings.Fields(text)[1:]) {    // Remove history line number
            newtext = newtext + " " + word                  //
        }

        // Redact credential patterns
        //

        text = newtext

        text = curl_u(text)
        text = https_creds(text)
        text = header_creds(text)

        fmt.Println(text)
    } 
```

如你所见，这个命令只涵盖了我在自己的历史课中发现的一些常见用例:

```
 1021  curl -v -H "Authorization: token 3067a4993bd73e857b72d716055bc137283b3a83" https://api.github.com/user
 1022  curl https://lbonanomi:3067a4993bd73e857b72d716055bc137283b3a83@api.github.com/user
 1023  curl -u :3067a4993bd73e857b72d716055bc137283b3a83 https://api.github.com/user 
```

转储历史并过滤上面的 go 代码，我们得到了这个不太有趣的结果:

```
 curl -v -H AUTH_HEADER_REDACTED https://api.github.com/user
 curl https://REDACTED:REDACTED@api.github.com/user
 curl -u :REDACTED https://api.github.com/user 
```

在每次执行之后通过这个命令来推送历史将会很麻烦，所以让我们调整一下我们的。bashrc 在每一行输入之后运行这个。每当用户在 shell 返回命令提示符之前按下 enter 键时，都会计算 bash 变量`$PROMPT_COMMAND`。虽然它最常见的用途是(可能)改变提示变量`$PS1`，但是它可以做任何我们想做的事情，包括执行一个 shell 函数，比如:

```
function sterilize_history() {
    history | $HOME/revisionist > sterile && history -r sterile && rm sterile
}

export PROMPT_COMMAND="sterilize_history" 
```

我相信一个更有经验的 go 程序员会发现这篇文章中代码的许多错误。在我新生的 go 知识库中发表一个措辞温和的问题会非常受欢迎。

这个想法最初是用一个 coreutils bash 函数探索出来的。