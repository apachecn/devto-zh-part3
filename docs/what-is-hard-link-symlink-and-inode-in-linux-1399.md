# Linux 中的硬链接、符号链接和索引节点是什么？

> 原文：<https://dev.to/setevoy/what-is-hard-link-symlink-and-inode-in-linux-1399>

[![](img/306c3bca0210fe58776a60aa6974ec2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jqxd9BXo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2016/01/What-is-it-e1452250201867.jpg) 在 Linux 中，*硬链接*，一般来说就是链接引用的同一个文件。

与硬链接相反——*符号链接*是一种“软链接”。

让我们检查一下细节，但首先要说几句关于 inodes 的话，因为它们是展示区别的最佳方式。

*这篇文章最初是[于 2013 年 8 月 13 日用俄语发表在](https://rtfm.co.ua/unix-chto-takoe-symlink-hardlink-i-inode/)的博客上。*

### 索引节点

显示差异的最简单方法是使用 inode–*索引描述符*。

`inode`是一个文件系统对象，包含关于拥有文件的用户和组、文件的权限、大小、类型、最后文件访问的时间戳( *`atime, access time`* )、其修改( *`mtime, modification time`* )、inode 本身的修改时间( *`ctime, changing time`* )以及到该文件的硬链接的计数器的信息。

在文件系统创建期间，每个 inode 对象都有自己唯一的 ID。

例如，让我们使用带有`-i`选项的`ls`实用程序来检查某个文件——这将向我们显示这些文件使用的索引节点的信息:

```
$ ls -lih | grep file
475949 -rw-r--r--  1 root     setevoy     0B Aug 13 11:51 file1
475950 -rw-r--r--  1 root     setevoy     0B Aug 13 11:51 file2
475951 -rw-r--r--  1 root     setevoy     0B Aug 13 11:51 file3 
```

*   第一列显示了每个文件的索引节点编号–*475949*。
*   第二列给出了关于文件权限的信息—*—-rw-r—r—*:所有者—*读* / *写*，组—*读*，其他—*读*。
*   这里的第三列是硬链接计数器—*1*。

接下来是所有者、组、大小、`mtime`和文件名。

#### 信息节点和“设备上没有剩余空间”问题

有点超出了本文的范围——但是与索引节点及其在文件系统中的角色有关。

有时可能会发生一件“奇怪”的事情:操作系统可以报告“*设备*上没有剩余空间”——但是`df`或`du`会显示一个分区上有很多可用的空闲空间。

一个可能的原因就是缺少索引节点:它们是在创建分区时创建的，数量有限。

因此，当一个分区上的所有索引节点都将被使用时，操作系统会告诉您“*设备*上没有剩余空间”。

您总是可以使用带有`-i`选项的`df`工具来检查可用的索引节点，例如:

```
$ df -i /dev/sdb4
Filesystem      Inodes IUsed   IFree IUse% Mounted on
dev            2042653   523 2042130    1% /dev 
```

这里——我可以在`/dev/sdb4`分区上拥有最多 *2.042.653* 个文件和/或目录。

但是现在我们感兴趣的是前一个主题中的`ls -lih`中的索引节点号和硬链接计数器，所以让我们进一步了解硬链接。

### 硬链接

那么——什么是“硬链接”?

事实上，这是同一个文件，只是名字不同。

让我们用`ln`工具创建一个新的硬链接。

它的语法是:

```
$ ln destitation_file hard_link 
```

例如–创建一个名为 *hardlink1* 的文件链接，它将指向名为 *file1* 的现有文件:

```
$ ln file1 hardlink1 
```

现在，为了确保它们是相同的文件系统对象，请检查它们的索引节点。在当前情况下，它是 *475949:*

```
$ ls -lih | grep 475949
475949 -rw-r--r--  2 root     setevoy     0B Aug 13 11:51 file1
475949 -rw-r--r--  2 root     setevoy     0B Aug 13 11:51 hardlink1 
```

如此处所示，我们有两个名称不同但索引节点编号相同的文件。

现在让我们再添加一个硬链接，并再次检查硬链接计数器:

```
$ ln file1 hardlink2
$ ls -lih | grep 475949
475949 -rw-r--r--  3 root     setevoy     0B Aug 13 11:51 file1
475949 -rw-r--r--  3 root     setevoy     0B Aug 13 11:51 hardlink1
475949 -rw-r--r--  3 root     setevoy     0B Aug 13 11:51 hardlink2 
```

现在是 3，而不是 2。

将这个计数器命名为“文件名计数器”比命名为“硬链接计数器”更好，因为实际上，我们有一个不同名称的对象。

### 符号链

现在让我们检查符号链接。

您可以使用带有`-s` ( *符号链接*)选项的同一个`ln`工具创建符号链接，语法如下:

```
$ ln -s file1 symlink1 
```

在这里我们创建了一个新的(！)文件名为*符号链接 1* 的文件系统对象，它指向已经存在的文件*文件 1* :

```
$ ls -lih | grep sym
475948 lrwxr-xr-x  1 root     setevoy     5B Aug 13 12:02 symlink1 -> file1 
```

请注意“权限属性”列开头的字母*l*——这里显示该文件是指向另一个文件的符号链接，您还可以在它的“名称”列中看到—*符号链接 1—>文件 1* 。

这里的`ls`可以显示的最常见属性有:

*   `-`:简单文件
*   `d`:目录
*   `l` : symlink
*   `s`:插座

现在让我们比较两个文件的索引节点号:

```
$ ls -li
...
475949 -rw-r--r--  3 root     setevoy       0 Aug 13 11:51 file1
...
475949 -rw-r--r--  3 root     setevoy       0 Aug 13 11:51 hardlink1
475949 -rw-r--r--  3 root     setevoy       0 Aug 13 11:51 hardlink2
...
475948 lrwxr-xr-x  1 root     setevoy       5 Aug 13 12:02 symlink1 -> file1 
```

它们是不同的，因为对于文件系统来说，它们是不同的对象，并且拥有一组专用的属性。

### 硬链接与符号链接的主要区别

现在让我们来看看这些文件系统对象之间的一些主要区别，以及它们在操作它们的方式上有什么不同。

*   硬链接不能指向另一个文件系统上的文件，因为信息节点不能属于另一个文件系统，但使用符号链接可以做到这一点
*   如果您要编辑一个硬链接——硬链接和目标文件都将被更改，但在使用符号链接的情况下——您可以更改符号链接的名称、属性，甚至删除或只是“更改方向”到另一个文件，而不会影响原始文件(但请记住，精确编辑符号链接将更改您的源文件——因为实际上您将编辑链接的对象)
*   你不能有到任何目录的硬链接——只能到一个普通的文件，查看[这个答案](https://askubuntu.com/a/525129/314659)了解更多细节

#### 删除链接

一些关于删除链接的话，因为这并不总是显而易见的。

何时删除硬链接——原始文件将一直存在，直到文件系统中至少有一个指向它的硬链接，但如果原始文件被删除，而在另一个位置仍有硬链接，则可以“改变”它的位置。

当你删除一个有符号链接的文件时——这个符号链接会被破坏，但会作为一个独立的对象保留在文件系统中。

让我们用一些例子来检查一下。

删除*文件 1* ，其中引用了*符号链接 1* 对象:

```
$ rm file1 
```

符号链接本身仍然存在:

```
$ ls -la | grep file1
lrwxr-xr-x   1 root     setevoy       5 Aug 13 12:02 symlink1 -> file1 
```

但是*文件 1* 不在:

```
$ file file1
file1: cannot open `file1' (No such file or directory) 
```

而*符号链 1* 现在坏了:

```
$ file symlink1
symlink1: broken symbolic link to `file1' 
```

希望这能帮助你获得更多关于文件系统和链接的知识。

### 类似的帖子

*   <small>08/13/2013</small>T2】什么是:что такое symlink，硬链接и inode в Linux？ <small>(7)</small>
*   t012/18/2013 t2unix:df 和 du-T3t 4 的不同值
*   t010/20/2018 t1t 2 Linux:在 ext4t 3 上增加无 LVM 分区的大小，同时保留数据
*   <small>03/18/2017</small>[【Linux:LVM】【home】，【root】](https://rtfm.co.ua/linux-lvm-umenshit-home-uvelichit-root/)<small></small>
<small>*   <small>2018 年 12 月 25 日</small> [Linux: добавить交换文件](https://rtfm.co.ua/linux-dobavit-swap-file/) <small>(0)</small></small>

<small></small>