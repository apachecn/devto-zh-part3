# 亲爱的日记:将 bash 会话记录为 Github Gists

> 原文：<https://dev.to/lbonanomi/dear-diary-recording-bash-session-as-github-gists-1nga>

您的雇主是否使用共享 Linux 帐户来管理基础架构或特定产品的各个方面？我知道我的许多雇主都有。你的雇主不鼓励或者完全阻止跑步吗？我知道我的雇主从来没有。

让我们试着在这个时髦的地方有点责任感。

我们需要功能来:

*   识别通过`sudo su`或`sudo bash`访问共享账户的用户。
*   捕获它们的 shell 会话输出。
*   为它们的会话输出使用防篡改存储。

### 首先，说几句关于安全的话

这个项目旨在成为一个*合作*团队的组织工具，而不是一个安全审计系统。

本文广泛使用了使用 [~/的认证。netrc 文件](https://linux.die.net/man/5/netrc)。你的系统管理员/公司安全人员/ *你*可能会反对~/。netrc 文件可能不安全。请至少使用一个*专用的* Github 令牌，它的[唯一作用域是【gist】](https://developer.github.com/apps/building-oauth-apps/understanding-scopes-for-oauth-apps/)并且请加密查看 GPG。netrc 文件。

### 查找支持的用户

> 告诉我你曾经是谁...
> <small>杀戮者，黑衣灵</small>

我们的第一步是确定用户是否已经进入当前的 shell，如果已经进入，他们的原始用户名是什么。确定用户是否被骗入 shell 的最简单的方法是比较他们的有效 UID 和真实 UID，知道`sudo`只修改调用者的有效 UID 值。

一个[独立脚本](https://github.com/lbonanomi/go/blob/master/suwho.go)可以比较 getuid()的结果和/dev/stdin 的所有权，以确定真实 uid 和有效 UID 是否匹配，但是为了可移植性，我们将坚持使用 coreutils。

`whoami`将可靠地返回与有效 UID 相关联的名称。
`who -m`将解析登录账户以获取认证登录的用户名(1)。

因此，我们对 su-ed 用户的简单测试可以是:

```
[[ $(who -m | awk '{ print $1 }') == $(whoami) ]] || echo "You are su-ed to $(whoami)" 
```

### 捕获会话

记录用户会话的明显选择是`script`命令，它将用户屏幕的内容记录到一个文件中。我们将使用'-q '标志来减少屏幕噪音。

捕获和发布 shell 会话的一个明显问题是，这会发布使用内联凭据的命令历史记录。即使您为私有存储调整了这里的函数，在共享空间中记录凭证似乎也是对信任的滥用，所以让我们运行一个(非常幼稚的)消毒。

```
function sterilizer() {
    cat $1 | strings | while read line
        do RAW="$line"
            echo "$RAW" | egrep -q "curl\b+.*(-u|--user).*:.*\b*" &&                RAW=$(echo "$RAW" | awk -F"http" '{ print "STERILIZED: curl http"$NF }')
            echo "$RAW" | egrep -q "curl.*(-H|--header).*(token|auth.*)\b+.*" &&    RAW=$(echo "$RAW" |awk -F"http" '{ print "STERILIZED: curl http"$NF }')
            echo "$RAW" | egrep -q "wget\b+.*--.*password\b+.*\b*" &&               RAW=$(echo "$RAW" |awk -F"http" '{ print "STERILIZED: wget http"$NF }')
            echo "$RAW" | egrep -q "https://(\S+?):\S+?@" &&                        RAW=$(echo "$RAW" |awk -F"@" '{ print "STERILIZED CALL TO: https://"$NF }')
            echo "$RAW"
        done > $1.dirty && mv $1.dirty $1
} 
```

没什么花哨(*或者全面！只是检查一下我在自己的命令历史中发现的泄露凭证的常见习惯用法。这个函数的速度太慢是可以原谅的，因为它只需要在每次会话捕获时运行一次。*

### 存储会话

为了减少开销 [Github gists](https://gist.github.com) 似乎是存储单个会话的最佳方式:

*   它们不需要专门的工具。
*   它们是离散的(每个用户的每个会话都可以单独存储)。
*   所有更改都会被记录并打上时间戳。
*   Github.com 比我自己管理的任何公司都更容易找到。

要点文件将需要来自`curl`的一点帮助来获得实例化...

```
$ curl -nsd '{"files":{"Filename":{"content":"Some content"}}}' https://api.github.com/gists 
```

但是一旦创建了 gist，就可以像处理任何其他 git 存储库一样处理它们。

```
$ git clone https://gist.github.com/dcf43b8ebb48d41090f3221f6bb4510d.git
Cloning into 'dcf43b8ebb48d41090f3221f6bb4510d'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done. 
```

### 综合起来看:

现在，我们有一个简单的测试来确认用户是否进入了 shell，一个命令来记录当前用户的会话，还有一些工具来创建和更新 Github.com 上的 gist 文件。让我们将这些片段组合到一个共享帐户的~/中。bash_profile。

```
#/bin/bash

function sterilizer() {
    cat $1 | strings | while read line
        do RAW="$line"
            echo "$RAW" | egrep -q "curl\b+.*(-u|--user).*:.*\b*" &&                RAW=$(echo "$RAW" | awk -F"http" '{ print "STERILIZED: curl http"$NF }')
            echo "$RAW" | egrep -q "curl.*(-H|--header).*(token|auth.*)\b+.*" &&    RAW=$(echo "$RAW" |awk -F"http" '{ print "STERILIZED: curl http"$NF }')
            echo "$RAW" | egrep -q "wget\b+.*--.*password\b+.*\b*" &&               RAW=$(echo "$RAW" |awk -F"http" '{ print "STERILIZED: wget http"$NF }')
            echo "$RAW" | egrep -q "https://(\S+?):\S+?@" &&                        RAW=$(echo "$RAW" |awk -F"@" '{ print "STERILIZED CALL TO: https://"$NF }')
            echo "$RAW"
        done > $1.dirty && mv $1.dirty $1
}

function script_to_github() {
    SCRIPTFILE_NAME="$1 su-ed to $2 on $(hostname)"

    # API token sanity-checks
    #
    curl -Insw "%{http_code}" https://api.github.com/user | tail -1 | grep -q 200 || exit

    # Instance a gist and clone it
    #
    GISTID=$(curl -nsd '{"files":{"sfile":{"content":"scriptfile in-flight"}}}' https://api.github.com/gists | awk -F"\"" '$2 == "id" {print $4}' | head -1);
    git clone -q "https://gist.github.com/"$GISTID".git" ~/$GISTID &>/dev/null;

    # API and gist can use the same token but need different addresses
    # confirm pushing-back a complete gist
    #
    if (cd ~/$GISTID && git push -q &>/dev/null || exit)
    then script -qf ~/$GISTID/sfile && (cd ~/$GISTID && sterilizer sfile && git add * && git commit -qm "." && git push -q origin 2>/dev/null && rm -rf ~/$GISTID/.git ~/$GISTID/sfile && rmdir ~/$GISTID);
        curl -nks -X PATCH -d '{"files":{"sfile":{"filename":"'$SCRIPTFILE_NAME'"}}}' https://api.github.com/gists/$GISTID >/dev/null
        kill -1 $PPID
    else
        # Can't push back the gist. Clean-up and don't bother the user.
        rm -rf ~/$GISTID/.git ~/$GISTID/sfile && rmdir ~/$GISTID
    fi
}

# who -m shows the real user, whoami shows the effective user.
# If these don't match, its most-likely the user is `sudo su`-ed.
#
# This chunk should kick-off session recording as required:

[[ $(who -m | awk '{ print $1 }') == $(whoami) ]] || script_to_github $(who -m | awk '{ print $1 }') $(whoami) 
```

现在我们应该有一个不引人注目的记录，记录了谁用一个共享的 Linux 帐户做了什么。