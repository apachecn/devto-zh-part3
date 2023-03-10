# 我的 Bash 提示

> 原文：<https://dev.to/jsrnop/my-bash-prompt-548l>

注意:这是从我的博客上转载的，有点过时，因为我现在主要使用 [Fish](https://fishshell.com/) ，但是还没有有趣的提示。

[![Bash Prompt Example](img/55aa0e52b10a029217a76f9580862331.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iqwQiU7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g4zyl5qxvnmlqqhed21h.png)

我最近更新了我的 Bash 提示符。它开始是因为我想将 git 状态信息合并到提示中。但是一旦我开始工作，结果发现我并不真的喜欢它，所以我放弃了它，但保留了我做的其他东西。

以下是我的 [`.bashrc`](https://github.com/markcaudill/dotfiles/blob/95111d3903e025d7d06e008f820a69401ff9ba57/bashrc#L12) :
中的相关部分

```
# Prompt
gen_prompt() {
    ## Looks like:
    ## ╭─┤pty0├─╢mark@bender:~/src╟─┤(✿◠‿◠)├─╮
    ## ╰>
    local __last_exit=$?
    [[ -f ~/.colors ]] && source ~/.colors

    local __line_color=${Red}
    local __prompt_color=${White}
    local __text_color=${White}

    local __success_string="${Green}(✿◠‿◠)"
    local __failure_string="${Red}(◡﹏◡✿)"
    local __exit_string=$(if [[ $__last_exit -ne 0 ]]; then echo $__failure_string; else echo $__success_string; fi)
    local __prompt="> "

    PS1="\n${__line_color}╭─┤${__text_color}\l${__line_color}├─╢${__text_color}\u@\h:\w${__line_color}╟─┤${__exit_string}${__line_color}├─╮\n╰${__prompt}\[$(tput sgr0)\]"
}
export PROMPT_COMMAND=gen_prompt 
```

Enter fullscreen mode Exit fullscreen mode

这里的 [`colors`](https://github.com/markcaudill/dotfiles/blob/aa7d94ebfb8c08a966a06267381f57660d4b7e3b/colors) 如果你想让颜色命令工作(保存到`~/.colors` ):

```
# Reset
    Color_Off="\[\033[0m\]"       # Text Reset

    # Regular Colors
    Black="\[\033[0;30m\]"        # Black
    Red="\[\033[0;31m\]"          # Red
    Green="\[\033[0;32m\]"        # Green
    Yellow="\[\033[0;33m\]"       # Yellow
    Blue="\[\033[0;34m\]"         # Blue
    Purple="\[\033[0;35m\]"       # Purple
    Cyan="\[\033[0;36m\]"         # Cyan
    White="\[\033[0;37m\]"        # White

    # Bold
    BBlack="\[\033[1;30m\]"       # Black
    BRed="\[\033[1;31m\]"         # Red
    BGreen="\[\033[1;32m\]"       # Green
    BYellow="\[\033[1;33m\]"      # Yellow
    BBlue="\[\033[1;34m\]"        # Blue
    BPurple="\[\033[1;35m\]"      # Purple
    BCyan="\[\033[1;36m\]"        # Cyan
    BWhite="\[\033[1;37m\]"       # White

    # Underline
    UBlack="\[\033[4;30m\]"       # Black
    URed="\[\033[4;31m\]"         # Red
    UGreen="\[\033[4;32m\]"       # Green
    UYellow="\[\033[4;33m\]"      # Yellow
    UBlue="\[\033[4;34m\]"        # Blue
    UPurple="\[\033[4;35m\]"      # Purple
    UCyan="\[\033[4;36m\]"        # Cyan
    UWhite="\[\033[4;37m\]"       # White

    # Background
    On_Black="\[\033[40m\]"       # Black
    On_Red="\[\033[41m\]"         # Red
    On_Green="\[\033[42m\]"       # Green
    On_Yellow="\[\033[43m\]"      # Yellow
    On_Blue="\[\033[44m\]"        # Blue
    On_Purple="\[\033[45m\]"      # Purple
    On_Cyan="\[\033[46m\]"        # Cyan
    On_White="\[\033[47m\]"       # White

    # High Intensty
    IBlack="\[\033[0;90m\]"       # Black
    IRed="\[\033[0;91m\]"         # Red
    IGreen="\[\033[0;92m\]"       # Green
    IYellow="\[\033[0;93m\]"      # Yellow
    IBlue="\[\033[0;94m\]"        # Blue
    IPurple="\[\033[0;95m\]"      # Purple
    ICyan="\[\033[0;96m\]"        # Cyan
    IWhite="\[\033[0;97m\]"       # White

    # Bold High Intensty
    BIBlack="\[\033[1;90m\]"      # Black
    BIRed="\[\033[1;91m\]"        # Red
    BIGreen="\[\033[1;92m\]"      # Green
    BIYellow="\[\033[1;93m\]"     # Yellow
    BIBlue="\[\033[1;94m\]"       # Blue
    BIPurple="\[\033[1;95m\]"     # Purple
    BICyan="\[\033[1;96m\]"       # Cyan
    BIWhite="\[\033[1;97m\]"      # White

    # High Intensty backgrounds
    On_IBlack="\[\033[0;100m\]"   # Black
    On_IRed="\[\033[0;101m\]"     # Red
    On_IGreen="\[\033[0;102m\]"   # Green
    On_IYellow="\[\033[0;103m\]"  # Yellow
    On_IBlue="\[\033[0;104m\]"    # Blue
    On_IPurple="\[\033[10;95m\]"  # Purple
    On_ICyan="\[\033[0;106m\]"    # Cyan
    On_IWhite="\[\033[0;107m\]"   # White 
```

Enter fullscreen mode Exit fullscreen mode