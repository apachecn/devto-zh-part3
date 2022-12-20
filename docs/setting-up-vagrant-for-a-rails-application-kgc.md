# 为 Rails 应用程序设置流浪者:第 1 部分

> 原文：<https://dev.to/denisepen/setting-up-vagrant-for-a-rails-application-kgc>

最近，我接受了一项技术挑战，要求我在一个隔离的环境中创建一个 rails 应用程序。我可以选择使用 Docker 容器或虚拟机。

我对这两种环境都没有任何经验，但是在做了一些研究之后，我决定使用虚拟机(vm)。虽然他们都执行相同的任务，但流浪者似乎需要更少的设置来开始。

首先，我下载了 VirtualBox，这是一个虚拟化引擎，流浪者将建立在其上。然后我就下载了流浪记。

然后，我创建了一个目录，它将成为我的流浪环境的根目录，并用 cd 光盘保存到这个目录中。

```
mkdir vagrant_demo
cd vagrant_demo 
```

要初始化此目录以便与 travel 一起使用，您必须运行“travel init ”,后跟您希望在您的 travel 环境中使用的基本操作系统的名称。我用“ubuntu/trusty64”创建了一个 Linux 环境。

```
vagrant init ubuntu/trusty64 
```

上面的命令在您的目录中创建一个流浪者文件。运行“漫游初始化”后，您会收到以下消息:

```
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant. 
```

这个浮动文件规定了如何配置您的浮动环境，并可以根据每个单独的应用程序进行更改。下面是我们刚刚创建的流浪者文件的副本:

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end 
```

这个文件是用 Ruby 编程语言编写的，正如你所看到的，它的大部分被注释掉了。唯一没有被注释掉的行是配置流浪盒(或操作系统)的行:

```
Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/trusty64"
end 
```

为了定义你的虚拟机的其他方面，你可以根据需要修改这个流浪者文件。如果没有进行修改，则使用默认设置。该文件中有 5 个规格可以修改:

- *config.vm.box* -这定义了你的虚拟机的操作系统，对于我们的应用程序，我们使用 ubuntu/trusty64。

- *config.vm.provider* -这为我们的流浪者环境定义了 vm 提供者或基础。我们用的是 VirtualBox。我们还可以修改虚拟机所需的内存和 cpu 数量。对于我的 rails 应用程序，我将内存从默认值增加到了 4G:

```
config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
end 
```

如果你需要更多的 cpu，你可以使用:

```
config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
end 
```

- *config.vm.network* -这是你的主机看到你的流浪汉盒子和应用的方式。您可以在这里为您的应用程序配置 ip 地址和端口。
由于 rails 应用程序默认端口为 3000，我更新了这个网络设置:

```
 config.vm.network "forwarded_port", guest: 3000, host: 3000 
```

其中来宾是虚拟机，主机是我们的计算机。

-*config . VM . synced . folder*-这是你定义虚拟机(你的流浪汉盒子)上的文件如何从主机(你的电脑)上访问文件的地方。这允许您在计算机上创建和修改项目文件，而不必在虚拟机中进行。如果需要的话，这些文件将会自动与流浪者盒子同步。我用了下面的:

```
config.vm.synced_folder ".", "/vagrant_files" 
```

第一个参数“.”指向*我们的电脑*上的当前目录或‘流浪者 _ 演示’目录。这是我们放置 rails 应用程序的地方，并告诉我们的流浪者盒子使用这些文件。第二个参数，“/vagger”是我们希望在 vm 上构建的目录，用来存放我们的 rails 应用程序。实际上，我们的文件既在我们的计算机上，也在虚拟机上，但我们可以在我们的计算机上更改它们，它们将自动保存并同步到虚拟机上的“/vagger _ files”目录中。您可以随意命名这个目录。为了简单起见，我只使用了“/流浪者 _ 文件”。

- *config.vm.provision* -这是游民档案的心脏和灵魂。这是我们定义如何设置虚拟环境的地方。我们可以指示虚拟机检索并安装任何应用程序、gem、模块或我们需要的任何东西。在上面自动生成的流浪者文件中我们看到了这样一个例子:

```
config.vm.provision "shell", inline: <<-SHELL
     apt-get update
     apt-get install -y apache2
   SHELL 
```

请注意，因为这是一台 linux 机器，所以我们可以使用普通的 linux 命令来定义和设置我们的环境。

**流浪汉文件的最终版本是:**

```
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/trusty64"
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  config.vm.synced_folder ".", "/vagrant_files"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
  end 
```

一旦我们定义了流浪者文件，我们就可以使用命令*流浪者 up* ，这将使我们的流浪者盒子启动并运行。

在我的下一篇博客[文章](https://dev.to/denisepen/setting-up-vagrant-for-a-rails-application-part-2--2gkm)中，我将描述如何将 rails 应用程序添加到你的 travel box 中。