# Azure 上的光子操作系统入门

> 原文：<https://dev.to/bureado/getting-started-with-photon-os-on-azure-32h8>

[Photon OS](https://vmware.github.io/photon/) 是一款开源的小型 Linux 容器主机，针对云原生应用进行了优化。在 Azure 上，它提供了一百多个包，包括 systemd、cloud-init 和 docker，但 Photon 在它们的 repos 中提供了一千多个包，如 Go。NET Core，Postgres，Tomcat，Zookeeper 或者 Kubernetes！

这是官方光子操作系统文档中 Azure quickstart 体验的简化版本。用 Photon OS 3.0 GA 和`azure-cli`2 . 0 . 38 版本测试。

## 先决条件

1.  从 [GitHub](https://github.com/vmware/photon/wiki/Downloading-Photon-OS) 下载 Azure 的 VHD，并在你的本地系统中用`tar xf`解压
2.  确保您的工作系统中有[最新的 Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) ( `az`)可用，并且您已经用`az login`登录

## 入门

就像任何其他定制的 Linux VHD 一样，您将创建几个工作区，上传 VHD 并基于它创建一个新的 VM。

[这里有一个简化虚拟机创建的脚本](https://gist.github.com/bureado/ff42e1c612a71a6fcd940a19845ef051)。它假设运行该脚本的任何用户的配置文件中都有一个 SSH 密钥对。它期望下载并提取的 VHD 作为参数，例如:`./script.sh photon-azure-3.0-26156e2.vhd` :

```
$ ./script.sh ./photon-azure-3.0-26156e2.vhd                                                      
+ set -e                                                                                                                  
+ GROUP=photon-rg                                                                                                         
+ STORAGE=phrg999                
+ LOCATION=southcentralus                                      
+ VM_NAME=photon-vm                                                           
+ STORAGE_CONTAINER=vhds                                                                                                  
+ IMAGE_PATH=./photon-azure-3.0-26156e2.vhd                                                                               
+ basename ./photon-azure-3.0-26156e2.vhd                                                                                
+ IMAGE_NAME=photon-azure-3.0-26156e2.vhd                                                                                 
+ az group create -n photon-rg -l southcentralus                     
+ az storage account create -n phrg999 -g photon-rg  
...                                  
+ az vm create -n photon-vm -g photon-rg --os-type linux --image https://phrg999.blob.core.windows.net/vhds/photon-azure-3.0-26156e2.vhd --use-unmanaged-disk --storage-account phrg999                                                             
{                                                          
...                     
  "powerState": "VM running",
... 
```

`az vm create`命令将输出(默认为 JSON 格式)关于新创建的虚拟机的信息，包括一个公共 IP 地址，您可以使用它通过 SSH 访问虚拟机。如果你错过了，运行`az vm list-ip-addresses -o table`。

```
bureado@photon4 [ ~ ]$ systemd-analyze 
Startup finished in 1.278s (kernel) + 2.177s (initrd) + 14.546s (userspace) = 18.002s
multi-user.target reached after 14.532s in userspace
bureado@photon4 [ ~ ]$ cloud-init --version
/usr/bin/cloud-init 18.3
bureado@photon4 [ ~ ]$ uname -a
Linux photon4 4.19.15-3.ph3 #1-photon SMP Mon Feb 25 14:48:35 UTC 2019 x86_64 GNU/Linux
bureado@photon4 [ ~ ]$ curl -s -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01" | jq .compute.location
"westus2"
bureado@photon4 [ ~ ]$ 
```

## 打扫卫生

如果您在上面的`$GROUP`变量中使用了唯一的资源组名，那么您可以通过运行`az group delete -g <group name> --yes`来删除所有资源。这将删除脚本创建的所有资源，包括上传的 blob，但不会将其从本地工作文件夹中删除。

玩得开心！