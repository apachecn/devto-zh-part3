# 在易受攻击的清单上查找主机

> 原文：<https://dev.to/erikaheidi/finding-hosts-on-ansible-inventories-3ojf>

在编写和测试教程时，我倾向于使用许多不同的服务器。尽管我总是将我的节点组织成组，并给它们起别名，以便于运行命令和剧本，但还是会有点混乱。我发现自己不得不一天多次打开或`cat`我的 Ansible 清单的内容，以获得我只知道别名的服务器的 IP 地址。

我试图找到一种自然、简洁的方式来获取这些信息，但我做不到。这是我想出来的:)

```
alias inventory="cat /etc/ansible/hosts | grep $1" 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当我需要从我的 Ansible 清单中的主机获取 IP 时，我只需运行:

```
$ inventory mysql 
```

Enter fullscreen mode Exit fullscreen mode

这是我得到的输出:

```
[mysqlservers]
mysql1 ansible_host=165.22.254.246 
```

Enter fullscreen mode Exit fullscreen mode

只需记住在您的`.basrc`(或者`.zshrc`，如果您像我一样使用 Zsh)文件中包含别名，这样别名就会被保留。