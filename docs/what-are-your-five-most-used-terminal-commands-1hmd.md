# 你最常用的五个终端命令是什么？

> 原文：<https://dev.to/ecologic/what-are-your-five-most-used-terminal-commands-1hmd>

**我开始:**

```
 1  1874  18.3942%     git
     2  709   6.95917%     sudo
     3  622   6.10522%     cd
     4  565   5.54574%     kubectl
     5  480   4.71143%     docker 
```

我在[秘密知识之书](https://github.com/trimstray/the-book-of-secret-knowledge/blob/master/README.md#list-of-commands-you-use-most-often)中发现了这块漂亮的宝石。(任何终端用户的好链接。看一看！)

**显示您最常用的 5 个命令，包括它们的用法%**

```
history | \
awk '{CMD[$2]++;count++;}END { for (a in CMD)print CMD[a] " " CMD[a]/count*100 "% " a;}' | \
grep -v "./" | \
column -c3 -s " " -t | \
sort -nr | nl |  head -n 5 
```

*(原文稍作修改，只显示前 5 名，而不是 20 名)*

我觉得在这里分享我们最常用的命令会很有趣。给我看看你的！；)

## 让我们连接！

我希望与其他技术爱好者一起拓展我的网络。让我们在这里或在 twitter 上联系吧！[👋@langhard](https://twitter.com/langhard)

## 平视

我的意思是我几乎不需要说这些，但是:**请在分享它们之前仔细看看你的命令，不要分享私人信息！**