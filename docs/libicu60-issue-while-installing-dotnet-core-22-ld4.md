# 在 Ubuntu 18.04 上安装 dotnet core 2.2 时出现 libicu60 问题

> 原文：<https://dev.to/ramx/libicu60-issue-while-installing-dotnet-core-22-ld4>

我试图在我的 Ubuntu 18.04 上安装 dotnet core 2.2，在浏览了[文档](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/sdk-current)后，我认为这可能很容易，但当我开始获得
时，我遇到了障碍

```
 # sudo apt-get install dotnet-sdk-2.2
Reading package lists... Done
Building dependency tree
Reading state information... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 dotnet-sdk-2.2 : Depends: aspnetcore-runtime-2.2 (>= 2.2.3) but it is not going to be installed
                  Depends: dotnet-runtime-2.2 (>= 2.2.3) but it is not going to be installed
E: Unable to correct problems, you have held broken packages. 
```

在尝试了所有其他方法后，我发现我必须安装`libicu60`

```
 sudo apt-get install libicu60
Reading package lists... Done
Building dependency tree
Reading state information... Done
Package libicu60 is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'libicu60' has no installation candidate 
```

所以最后我必须手动下载并安装`libicu60`

```
wget http://mirrors.kernel.org/ubuntu/pool/main/i/icu/libicu60_60.2-3ubuntu3_amd64.deb

sudo dpkg -i libicu60_60.2-3ubuntu3_amd64.deb 
```

现在我可以成功安装它了

```
sudo apt-get update

 sudo apt-get install dotnet-sdk-2.2
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  aspnetcore-runtime-2.2 dotnet-host dotnet-hostfxr-2.2 dotnet-runtime-2.2 dotnet-runtime-deps-2.2
  liblttng-ust-ctl2 liblttng-ust0 liburcu4
The following NEW packages will be installed:
  aspnetcore-runtime-2.2 dotnet-host dotnet-hostfxr-2.2 dotnet-runtime-2.2 dotnet-runtime-deps-2.2
  dotnet-sdk-2.2 liblttng-ust-ctl2 liblttng-ust0 liburcu4
0 upgraded, 9 newly installed, 0 to remove and 63 not upgraded.
Need to get 133 MB of archives.
After this operation, 387 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://archive.ubuntu.com:80/ubuntu xenial/universe amd64 liburcu4 amd64 0.9.1-3 [47.3 kB]
Get:2 https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic/main amd64 dotnet-runtime-deps-2.2 amd64 2.2.3-1 [2,602 B]
Get:3 https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic/main amd64 dotnet-host amd64 2.2.3-1 [36.4 kB]
Get:4 https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic/main amd64 dotnet-hostfxr-2.2 amd64 2.2.3-1 [144 kB]
Get:5 https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic/main amd64 dotnet-runtime-2.2 amd64 2.2.3-1 [20.7 MB]
Get:6 http://archive.ubuntu.com:80/ubuntu xenial/universe amd64 liblttng-ust-ctl2 amd64 2.7.1-1 [72.2 kB]
Get:7 http://archive.ubuntu.com:80/ubuntu xenial/universe amd64 liblttng-ust0 amd64 2.7.1-1 [127 kB]
Get:8 https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic/main amd64 aspnetcore-runtime-2.2 amd64 2.2.3-1 [22.8 MB]
Get:9 https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic/main amd64 dotnet-sdk-2.2 amd64 2.2.105-1 [88.6 MB]
Fetched 133 MB in 42s (3,151 kB/s)
Selecting previously unselected package liburcu4:amd64.
(Reading database ... 77153 files and directories currently installed.)
Preparing to unpack .../liburcu4_0.9.1-3_amd64.deb ...
Unpacking liburcu4:amd64 (0.9.1-3) ...
Selecting previously unselected package liblttng-ust-ctl2:amd64.
Preparing to unpack .../liblttng-ust-ctl2_2.7.1-1_amd64.deb ...
Unpacking liblttng-ust-ctl2:amd64 (2.7.1-1) ...
Selecting previously unselected package liblttng-ust0:amd64.
Preparing to unpack .../liblttng-ust0_2.7.1-1_amd64.deb ...
Unpacking liblttng-ust0:amd64 (2.7.1-1) ...
Selecting previously unselected package dotnet-runtime-deps-2.2.
Preparing to unpack .../dotnet-runtime-deps-2.2_2.2.3-1_amd64.deb ...
Unpacking dotnet-runtime-deps-2.2 (2.2.3-1) ...
Selecting previously unselected package dotnet-host.
Preparing to unpack .../dotnet-host_2.2.3-1_amd64.deb ...
Unpacking dotnet-host (2.2.3-1) ...
Selecting previously unselected package dotnet-hostfxr-2.2.
Preparing to unpack .../dotnet-hostfxr-2.2_2.2.3-1_amd64.deb ...
Unpacking dotnet-hostfxr-2.2 (2.2.3-1) ...
Selecting previously unselected package dotnet-runtime-2.2.
Preparing to unpack .../dotnet-runtime-2.2_2.2.3-1_amd64.deb ...
Unpacking dotnet-runtime-2.2 (2.2.3-1) ...
Selecting previously unselected package aspnetcore-runtime-2.2.
Preparing to unpack .../aspnetcore-runtime-2.2_2.2.3-1_amd64.deb ...
Unpacking aspnetcore-runtime-2.2 (2.2.3-1) ...
Selecting previously unselected package dotnet-sdk-2.2.
Preparing to unpack .../dotnet-sdk-2.2_2.2.105-1_amd64.deb ...
Unpacking dotnet-sdk-2.2 (2.2.105-1) ...
Processing triggers for libc-bin (2.23-0ubuntu10) ...
Setting up liburcu4:amd64 (0.9.1-3) ...
Setting up liblttng-ust-ctl2:amd64 (2.7.1-1) ...
Setting up liblttng-ust0:amd64 (2.7.1-1) ...
Setting up dotnet-runtime-deps-2.2 (2.2.3-1) ...
Setting up dotnet-host (2.2.3-1) ...
Setting up dotnet-hostfxr-2.2 (2.2.3-1) ...
Setting up dotnet-runtime-2.2 (2.2.3-1) ...
Setting up aspnetcore-runtime-2.2 (2.2.3-1) ...
Setting up dotnet-sdk-2.2 (2.2.105-1) ...
This software may collect information about you and your use of the software, and send that to Microsoft.
Please visit http://aka.ms/dotnet-cli-eula for more information.
Welcome to .NET Core!
---------------------
Learn more about .NET Core: https://aka.ms/dotnet-docs
Use 'dotnet --help' to see available commands or visit: https://aka.ms/dotnet-cli-docs

Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. The data is anonymous and doesn't include command-line arguments. The data is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_CLI_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.

Read more about .NET Core CLI Tools telemetry: https://aka.ms/dotnet-cli-telemetry

Configuring...
--------------
A command is running to populate your local package cache to improve restore speed and enable offline access. This command takes up to one minute to complete and only runs once.
Processing triggers for libc-bin (2.23-0ubuntu10) ... 
```

希望这有所帮助！

干杯！！