# 关于 Perl 和不匹配的二进制文件

> 原文：<https://dev.to/foursixnine/about-perl-and-mismatched-binaries-346l>

# 恐怖

您碰巧更新了您的系统(在我的例子中，我使用 Tumbleweed 或 Gentoo ),并且有了新版本的 Perl，在某个时候，您意识到您正在使用 [local::lib](https://metacpan.org/pod/local::lib) ,痛苦随之而来:启动了一个 shell，您发现了一个可怕的:

```
Cwd.c: loadable library and perl binaries are mismatched (got handshake key 0xdb00080, needed 0xdb80080) 
```

这意味着:该模块(这里是 Cwd)与您系统上安装的当前版本的 perl 不兼容(因为它是一个 [XS 模块](http://modernperlbooks.com/mt/2009/05/perl-5-and-binary-compatibility.html)):很可能它是为以前的版本编译的，导致那些[二进制文件不匹配](https://rt.perl.org/Public/Bug/Display.html?id=133440)

## 不要慌！

在过去，我曾经重新安装完整的 local::lib 目录，然而在 irc.perl.org 的#toolchain 上闲逛并问了几个问题后，我开始编写(或者说是破解，一个丑陋的破解)一个快速的 perl 脚本来遍历已经安装的 [local::lib](https://github.com/foursixnine/stunning-octo-chainsaw/blob/master/ll-walker.pl) 包，只查看指定的目录…它工作得很好，给了我需要的列表，以便我可以稍后重新安装，然而 Grinnz 把我指向了他的 [perl-migrate-modules](https://bit.ly/2CwLXvb) 脚本， 聊了一会儿之后，他添加了`from`开关，允许人们重新安装所有出现在旧的 local::lib 目录中的模块:

## 光明

```
# Migrate modules from an old (inactive) local::lib directory
$ perl-migrate-modules --from ~/perl5-old/lib/perl5 /usr/bin/perl 
```

希望你觉得有用:)