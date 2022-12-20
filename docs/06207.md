# 访问控制列表- ACL

> 原文：<https://dev.to/rodrigosyscop/access-control-list-acl-1im2>

为了改进众所周知的 Linux 权限模式`ugo` / `rwx`，允许我们为不同的用户或组设置不同的权限，我们可以利用访问控制列表- ACL。

## 要求

存储要设置 ACL 的文件的文件系统必须安装有 ACL 支持。您可以通过运行:
来检查

```
mount /dev/xvda1 | grep attr
/dev/xvda1 on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota) 
```

我们可以看到`attr2`,它表明这个文件系统支持扩展属性——ACL。如果您没有看到您的文件系统的那个选项，或者如果您看到了`noacl`，您可以在`/etc/fstab`中修改它，添加或删除适当的选项，并重新挂载文件系统:

```
mount /dev/xvda1 -o remount 
```

> 您不能重新挂载根`/`文件系统。你必须重启你的机器来启用新的选项。

## 使用 ACL

假设我们有这两个组和 5 个用户:

*   `devs` : `euler`，`colleen`，`eric`
*   `ops` : `rodrigo`，`jonas`

我们有一个项目文件夹，`devs`对它有完全的访问权限:

```
groupadd dev
groupadd ops
useradd euler
useradd colleen
useradd rodrigo
useradd jonas
usermod -aG devs euler
usermod -aG devs colleen
usermod -aG devs eric
usermod -aG ops rodrigo
usermod -aG ops jonas

mkdir /var/projectX
touch /var/projectX/main.py

chown -R euler.devs /var/projectX
chmod -R 770  /var/projectX 
```

但是，如果我们想授予不在`devs`组中的用户写访问权限呢？
我们可以创建一个包含所有必要用户的新组，但这会很快变得混乱。

使用 ACL，我们可以授予个人用户访问文件和目录的权限。因此，要为 jonas 添加对文件
的写权限

```
setfacl -m u:jonas:rw /var/projectX/main.py
setfacl -m u:jonas:rx /var/projectX 
```

好了，`jonas`现在可以读取和进入`/var/projectX`文件夹，也可以写入`main.py`。

我们可以通过运行`getfacl`命令:
来检查文件的 ACL

```
 getfacl /var/projectX/main.py

getfacl: Removing leading '/' from absolute path names
# file: var/projectX/main.py
# owner: euler
# group: dev
user::rwx
user:jonas:rw-
group::rwx
mask::rwx
other::--- 
```

我们可以删除上面的 ACL，用`-m`替换`-x`，或者使用`-b`删除文件或目录中的所有 ACL:

```
setfacl -b /var/projectX 
```

我希望你已经了解了一点 Linux ACLs，你可以在[设置访问 ACL](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/s1-acls-setting)中了解更多。