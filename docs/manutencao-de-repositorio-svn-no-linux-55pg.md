# 维护 Linux 上的 SVN 存储库

> 原文：<https://dev.to/pcollares/manutencao-de-repositorio-svn-no-linux-55pg>

尽管 GIT 已经占据了版本控制市场的支配地位，SVN 仍然得到广泛使用，因此，我带来了本简短教程，其中介绍了可以在此工具中执行的主要操作。

为了进行演示，我将假定我们有一个名为“”的项目，该项目位于本地网络上的一个存储库中，地址为:“”，我将在/home/pcollas/下的我的主目录中工作。

我还会认为最初的结构已经建立，如下所述:

```
my-project
├─ branches 
├─ tags
└─ trunk 
```

## 签出存储库

导航到要下载存储库的目录:

```
$ cd /home/pcollares 
```

运行命令进行结帐:

```
$ svn co https://192.168.1.20/svn/my-project 
```

## 创建新行业

导航到存储库的根目录:

```
$ cd /home/pcollares/my-project 
```

环绕行业创建命令:

```
$ svn copy https://192.168.1.20/svn/my-project/trunk/ /home/pcollares/my-project/branches/[nome_do_branche] 
```

提交行业创建:

```
$ cd branches/[nome_do_branche]

$ svn commit -m "Criação do branche [nome_do_branche] 
```

## 用干线变更更新一个分支

导航到行业:

```
$ cd /home/pcollares/my-project/branches/[nome_do_branche] 
```

更新行业:

```
$ svn update 
```

测试可能的冲突:

```
$ svn merge --dry-run https://192.168.1.20/svn/my-project/trunk 
```

与业界有效地环绕合并:

```
$ svn merge https://192.168.1.20/svn/my-project/trunk 
```

确认行业:

```
$ svn commit -m "Merge entre o trunk e o branche [nome_do_branche]" 
```

## 一个行业的再融合

导航至中继:

```
$ cd /home/pcollares/my-project/trunk/ 
```

升级干线:

```
$ svn update 
```

测试与*干运行*的可能冲突:

```
$ svn merge --reintegrate --dry-run https://192.168.1.20/svn/my-project/branches/[nome_do_branche] 
```

有效地包围重返社会的干线:

```
$ svn merge --reintegrate https://192.168.1.20/svn/my-project/branches/[nome_do_branche] 
```

解决可能的冲突。

提交中继:

```
$ svn commit -m "Reintegração do Branche [nome_do_branche] com o Trunk" 
```

由于本开发行已结束，删除本行业:

```
$ svn delete https://192.168.20.1/svn/my-project/branches/[nome_do_branche] -m "Remoção do branche [nome_do_branche] após a reintegração com o trunk" 
```

删除行业本地目录:

```
$ rm -Rf /home/pcollares/my-project/branches/[nome_do_branche] 
```

## 创建标记

导航到项目目录:

```
$ cd /home/pcollares/my-project/ 
```

Crie a nova tag a partir do trunk:

```
$ svn copy https://192.168.1.20/svn/my-project/trunk/ tags/[nome_da_tag] 
```

确认标签创建:

```
$ cd tags

$ svn commit -m "Fechamento da criação da tag [nome_da_tag]" 
```

## 参考文献

[http://SVN book . red-bean . com/en/1.7/SVN . branch merge . basicmerging . html](http://svnbook.red-bean.com/en/1.7/svn.branchmerge.basicmerging.html)

[]的