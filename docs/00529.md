# 用 Ansible 将 PHP 升级到 7.3

> 原文：<https://dev.to/narongdejsrn/upgrading-php-to-7-3-with-ansible-4fp1>

在我的公司，我们的服务之一是为客户开发和维护 Wordpress 网站。我们使用 Digitalocean 托管客户的网站，我们希望能够轻松地分离客户环境，因此我们为每个客户使用不同的 droplet。但是，来自数字海洋图像的预装版本是 7.0.X，已被弃用。

## 由 Ansible 保存

Ansible 是 Redhat 开发的 IT 自动化工具，可以让您轻松地自动化 IT 任务。我有没有提到，**它是免费的**

虽然您可以使用 Ansible 做很多事情，但我们将只关注在我们的服务器上升级 PHP。之后一定要看看你能用 ansible 做些什么。

## 安装

首先，你必须安装一个可转换的控制节点。在更大的范围内，这将是在一个通过 SSH 管理其他 ansible 节点(其他服务器)的服务器上。然而，对于我们的情况来说；我们将使用自己的计算机作为控制节点。

Ansible 可以在任何可以运行 Python 2 或 3、**的机器上工作，除了 Windows** 。如果你运行的是 Windows，使用 Linux 子系统或者其他什么。

对于 Mac 用户来说，首选的方式是使用 pip
安装 ansible

```
pip install --user ansible 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用的是 Ubuntu 16.04 或 18.04，运行下面的命令

```
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible 
```

Enter fullscreen mode Exit fullscreen mode

对于其他发行版或操作系统，请查看官方安装教程

## 设置您的库存

在 Ansible 中，您可以在位于`/etc/ansible/hosts`的清单文件中确定服务器的位置

创建您的库存文件

```
[wordpress]
website-a ansible_host=122.X.X.X ansible_port=2222 ansible_user=someuser
website-b ansible_host=122.X.X.X 
```

Enter fullscreen mode Exit fullscreen mode

上面，你创建了一个名为 *wordpress* 的主机组，它包含网站 a 和网站 b

您也可以设置 *ansible_password=* ,但是我建议您设置公钥

## 创建您的行动手册

接下来，我们将创建一个编排文件来告诉 ansible 该做什么。创建一个文件名`upgrade-php.yml`

```
- hosts: wordpress
  vars:
    php_version: '7.3'
    php_versions_install_recommends: false
  roles:
    - role: geerlingguy.repo-remi
      when: ansible_os_family == 'RedHat'
    - geerlingguy.php-versions
    - geerlingguy.php 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的是我们告诉 ansible 运行一组任务(角色)geerlingguy.php 版本，和 geerlingguy.php。

就像 NPM 或皮普，你需要从网上回购下载软件包，以便使用它。运行这些命令

```
ansible-galaxy install geerlingguy.repo-remi
ansible-galaxy install geerlingguy.php-versions
ansible-galaxy install geerlingguy.php 
```

Enter fullscreen mode Exit fullscreen mode

清除旧的 PHP 版本，添加适当的存储库，安装你的 PHP 并安装所有的推荐模块。你可以在这里回顾一下[的作用](https://github.com/geerlingguy/ansible-role-php-versions/blob/master/tasks/main.yml)

## 升级服务器 PHP

最后，我们准备在我们的服务器上升级 PHP。运行该命令:

```
ansible-playbook upgrade-php.yml 
```

Enter fullscreen mode Exit fullscreen mode

等待剧本完成，你的 PHP 版本应该升级到 7.3🎉🎉🎉

Ansible 有很多更棒的特性，可以让你的系统管理员的普通任务在一个命令中消失。我对 ansible 不熟悉，希望早点了解它。如果你有什么酷的可行项目，请在评论中告诉我。