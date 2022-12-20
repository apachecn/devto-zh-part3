# 为 Rails 应用程序设置流浪者:第 2 部分

> 原文：<https://dev.to/denisepen/setting-up-vagrant-for-a-rails-application-part-2--2gkm>

在之前的[帖子](https://dev.to/denisepen/setting-up-vagrant-for-a-rails-application-kgc)中，我们配置了 travel 文件，准备使用 travel box 托管 rails 应用程序。在这篇文章中，我将介绍如何配置流浪者盒子，并把你的 rails 应用程序加入其中。

创建好 travel 文件后，您可以简单地运行*travel up*来启动它。要进入它，只需使用命令*流浪者 ssh* ，你会看到你现在在你的流浪者盒子里。我们现在处于一个功能齐全但又相互隔离的操作系统中。

### 配置流浪箱

因为我计划在这个环境中运行一个 rails 应用程序，所以我需要添加运行 rails 所需的所有东西:git、rvm、ruby、rails、nodejs 和 Postgresql。我从这篇[文章](http://tutorials.jumpstartlab.com/topics/vagrant_setup.html)中获得了设置 git、rvm、ruby 和 rails 的指导。

Install git:

```
sudo apt-get update
sudo apt-get install git 
```

安装卷曲:

```
sudo apt-get install curl
\curl -sSL https://get.rvm.io | bash 
```

加载 rvm:

```
source /home/vagrant/.rvm/scripts/rvm
rvm requirements 
```

安装你选择的任何版本的 Ruby:

```
rvm install 2.1 
```

设置您的 Ruby 默认版本:

```
rvm use 2.1 --default 
```

验证您的 Ruby 版本:

```
ruby -v 
```

安装导轨:

```
gem install rails 
```

检查您的 rails 版本:

```
rails -v 
```

安装捆扎机:

```
gem install bundler 
```

捆绑你的宝石:

```
bundle install 
```

安装节点 js:

```
sudo apt-get install nodejs 
```

安装你的数据库——我用的是 Postgresql

```
$ sudo apt-get install postgresql libpq-dev
$ sudo su - postgres
$ psql

    postgres=# CREATE USER vagrant;
    postgres=# CREATE DATABASE your_database_name;
    postgres=# GRANT ALL PRIVILEGES ON DATABASE "your_database_name" to vagrant;
    postgres=# \q
$ exit 
```

上面创建的数据库应该与 rails 应用程序中使用的数据库同名。数据库名称在 database.yml 文件中设置。
例如，我的应用程序的 database.yml 文件有如下设置:

```
default: &default
    adapter: postgresql
    pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

development:
    adapter: postgresql
    username: vagrant
    database: main_rest_development

test:
    adapter: postgresql
    username: vagrant
    database: main_rest_test

production:
  <<: *default
  database: main_rest_production 
```

请记住，该文件中文本的**对齐必须完美，否则将无法正确读取！**

一旦所有这些都完成了，你的流浪环境就配置好了，可以运行你的应用程序了。

## 从头开始创建应用程序

如果您将从头开始创建一个应用程序，您将需要在您的本地机器上这样做。

### 在您的本地机器上执行-非流浪者

您应该在您的终端中打开一个新的标签页，该标签页位于创建您的流浪者文件的目录中。

要创建新的应用程序，只需运行*rails new your-app-name-database = PostgreSQL*。

这将在此目录中创建一个新的应用程序，并且应该使用您在上面分配的名称和权限来配置数据库。

### 在流浪中演出

要运行这个应用程序，你必须进入另一个终端标签，流浪者盒子正在运行。将(cd)移动到“/vagger _ file”目录(或您在 vagger 文件中指定为同步文件夹的任何目录)。移动(cd)到新应用程序的目录中。运行*‘捆绑安装’*。然后使用 *rails s* 启动 rails 服务器。您的应用程序现在应该在您指定的端口或 url 上运行。

## 使用现有的应用程序

这也必须在本地机器上执行。

### 在您的本地机器上执行-非流浪者

您应该在您的终端中打开一个新的标签页，该标签页位于创建您的流浪者文件的目录中。如果您有一个想要使用的现有应用程序，您可以简单地将代码从 github 克隆到您的本地机器:

```
git clone your_existing_app 
```

您需要更新 database.yml 文件以获得正确的数据库和数据库名称。

### 在流浪中演出

要运行这个应用程序，你必须进入另一个终端标签，流浪者盒子正在运行。

将(cd)移动到“/vagger _ file”目录(或您在 vagger 文件中指定为同步文件夹的任何目录)。

移动(cd)到新应用程序的目录中。

运行*‘捆绑安装’*。

然后使用 *rails s* 启动 rails 服务器。您的应用程序现在应该在您指定的端口或 url 上运行。

当你运行完你的应用程序，你可以停止它。进入流浪者的根文件，输入*退出*。这将把你放回你的本地机器。

在你的本地机器上输入*流浪者摧毁*，你的箱子现在停止了。

为了运行你的应用程序，配置一个 travel box 需要做大量的工作。但是，一旦配置好，这个框就可以用于将来需要相同设置的任何其他 rails 应用程序。如果需要，还可以将其复制并移动到其他系统。流浪者提供完全隔离，这将使你的应用程序非常需要的稳定性。