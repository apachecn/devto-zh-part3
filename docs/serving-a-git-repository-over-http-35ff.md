# 通过 HTTP 服务 Git 存储库

> 原文：<https://dev.to/mohammadaltaleb/serving-a-git-repository-over-http-35ff>

不久前，我被分配了一项任务，研究如何通过 HTTP 服务 Git 存储库。我找到的教程和文档要么杂乱无章，要么不完整。

经过一个痛苦的过程，我让它工作了。但是，不幸的是，我们决定采用不同的方法来进行回购。

我不想就这样放弃努力。所以，这里有一篇关于它的博文。

# 设置

我们将使用 [Apache](https://httpd.apache.org) web 服务器为存储库提供服务，并将它安装在一台 [Ubuntu 16.04.5](http://releases.ubuntu.com/16.04/) 机器上(在我的例子中是 [AWS EC2](https://aws.amazon.com/ec2/) 实例)。

# 安装

让我们从安装 Git 和 Apache
开始

```
sudo apt-get update
sudo apt-get install git-core
sudo apt-get install apache2 
```

Enter fullscreen mode Exit fullscreen mode

# 创建回购

对于本教程，我们将在文档根( **/var/www/html** )
中创建一个新的 Git 存储库

```
cd /var/www/html
mkdir git
cd git
mkdir reponame
cd reponame
git init --bare
chgrp -R www-data /var/www/html/git
chown -R www-data /var/www/html/git 
```

Enter fullscreen mode Exit fullscreen mode

如果你想让其他人推送回购协议，打开 Git 添加到存储库中的名为 **config** 的文件，并在其中添加以下内容

```
[http]
    receivepack = true 
```

Enter fullscreen mode Exit fullscreen mode

# 认证

我们只希望经过身份验证的用户能够访问回购。因此，首先我们将在( **/usr/local/bin/** ):
中创建一个名为 **auth.sh** 的虚拟 bash 脚本

```
cd /usr/local/bin/
touch auth.sh
chmod 755 auth.sh 
```

Enter fullscreen mode Exit fullscreen mode

该脚本将检查给定的用户名和密码，如果正确，则用 **0** 代码退出，否则用 **1** 代码退出。
剧本内容如下:

```
#! /bin/bash
read user
read password

if [[ ("$user" = "hello") && ("$password" = "world") ]]; then exit 0
else exit 1
fi 
```

Enter fullscreen mode Exit fullscreen mode

为了能够在 Apache 中使用我们的脚本，我们应该安装软件包[libapache 2-mod-authnz-external](https://packages.ubuntu.com/xenial/libapache2-mod-authnz-external)，它允许对外部服务
进行认证

```
apt-get install libapache2-mod-authnz-external 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将确保认证模块被启用

```
a2enmod authnz_external 
```

Enter fullscreen mode Exit fullscreen mode

# 配置 Apache

在( **/etc/apache2/** )
中创建一个名为 **httpd.conf** 的 Apache 配置文件

```
cd /etc/apache2/
touch httpd.conf 
```

Enter fullscreen mode Exit fullscreen mode

**httpd.conf** 的内容应该是这样的:

```
<VirtualHost *:80>

    SetEnv GIT_PROJECT_ROOT /var/www/html/git
    SetEnv GIT_HTTP_EXPORT_ALL
    SetEnv REMOTE_USER=$REDIRECT_REMOTE_USER

    DocumentRoot /var/www/html
    <Directory "/var/www/html">
        Options All Includes Indexes FollowSymLinks
        Order allow,deny
        Allow from all
        AllowOverride All
    </Directory>

    ScriptAliasMatch \
        "(?x)^/git/(.*/(HEAD | \
        info/refs | \
        objects/(info/[^/]+ | \
        [0-9a-f]{2}/[0-9a-f]{38} | \
        pack/pack-[0-9a-f]{40}\.(pack|idx)) | \
        git-(upload|receive)-pack))$" \
        "/usr/lib/git-core/git-http-backend/$1"

    Alias /git /var/www/html/git

    <Directory /usr/lib/git-core>
        Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
        AuthType Basic
        AuthName "Restricted"
        AuthBasicProvider external
        AuthExternal auth
        require valid-user
    </Directory>

    <Directory /var/www/html/git>
        Options +ExecCGI +Indexes +FollowSymLinks
        Allowoverride None
        AuthType Basic
        AuthName "Restricted"
        AuthBasicProvider external
        AuthExternal auth
        require valid-user
    </Directory>

    AddExternalAuth auth /usr/local/bin/auth.sh
    SetExternalAuthMethod auth pipe

</VirtualHost> 
```

Enter fullscreen mode Exit fullscreen mode

现在，打开**/etc/Apache 2/Apache 2 . conf**文件并注释掉该行:

```
IncludeOptional sites-enabled/*.conf 
```

Enter fullscreen mode Exit fullscreen mode

然后，添加一行:

```
Include /etc/apache2/httpd.conf 
```

Enter fullscreen mode Exit fullscreen mode

启用 **mod_cgi** 、 **mod_alias** 和 **mod_env** 、T6】

```
a2enmod cgi alias env 
```

Enter fullscreen mode Exit fullscreen mode

最后，更新 repo 并重启 Apache

```
cd /var/www/html/git/reponame
git update-server-info

service apache2 restart 
```

Enter fullscreen mode Exit fullscreen mode

# 测试

在您的机器上，克隆存储库

```
git clone http://{SERVER_DOMAIN}/git/reponame/ 
```

Enter fullscreen mode Exit fullscreen mode

向回购协议中添加内容并提交

```
touch test.txt
git add test.txt 
git commit -m "test commit" 
```

Enter fullscreen mode Exit fullscreen mode

将更改推送到远程存储库

```
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。如果克隆和推送成功，这意味着我们成功地服务了我们的存储库。