# 在 Azure 托管的 Bits 上创建一个大规模可扩展的 WordPress 站点

> 原文：<https://dev.to/robconery/creating-a-massively-scalable-wordpress-site-on-azures-hosted-bits-3o21>

前几天，我和一个朋友在我的合作空间(檀香山的 Impact Hub)又进行了一次关于 WordPress 的讨论。我告诉他们我正在开发一个部署脚本，在 Azure 的基础设施上运行 WordPress 和 Ghost，他们的反应和我习惯的一样:

> WordPress？伙计…这个新工作让你神魂颠倒了吗？

可能是吧。我的回答是我一贯给出的回答的变体:

> WordPress [为整个网络](https://venturebeat.com/2018/03/05/wordpress-now-powers-30-of-websites/)的 30%供电。我认为在 Azure 上支持它是显而易见的。

我用 WordPress。我喜欢它，是的，作为一名开发者，我希望有些事情可以有所不同。然而，事情的简单性才是真正吸引我的地方。所以，如果你是一个使用 WordPress 并且也使用 Azure 的人，这篇文章可能会很有用！

_ **披露**:我在微软从事开发者关系工作。我的工作是找出这样的问题，然后 1)告诉产品团队哪些地方可以做得更好，2)向您报告我是如何设法让这些工作正常进行的。如果这有帮助，万岁！_

## 第 0 步:熟悉 CLI

有其他方法可以做到这一点，但我喜欢“照本宣科的食谱”。也有 ARM (Azure 资源管理)模板，允许你点击一个按钮并“部署到 Azure ”,我认为这很棒，但我有点控制狂，喜欢我的 shell 脚本(这可能不是最好的，所以如果你看到改进，请大声说出来)。

我喜欢 shell 脚本的另一个原因是，您可以添加注释，并将它们用作一种学习工具。至少这是我希望在这里做的——学习 Azure 对我来说并不容易，希望我的笔记能帮助到你们中的一些人。

如果你想直接进入主题，这里有一个要点，你可以通读一下。**是的，我知道，**我只要用 Docker 编写文件就可以了。我想要一个更好的数据库解决方案，我也想要挑战…和你一起回到桥下！

最后——如果你想继续玩下去，确保你已经安装了[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest),并且[使用默认订阅设置登录](https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

我们需要做的第一件事是声明我们的变量，等等:

```
RG="[YOUR RESOURCE GROUP]"
APPNAME=$RG-wordpress #Name what you want
LOCATION="Central US" #put where you like

#Recommend to keep these random, but if you need to change go for it
USER=admin_$RANDOM #set this to whatever you like but it's not something that should be easy
PASS=$(uuidgen) #Again - whatever you like but keep it safe! Better to make it random
SERVERNAME=server$RANDOM #this has to be unique across azure

#accepted values for the service plan: B1, B2, B3, D1, F1, FREE, P1, P1V2, P2, P2V2, P3, P3V2, PC2, PC3, PC4, S1, S2, S3, SHARED
#B2 is good for getting started - read up on the different levels and their associated cost.
PLAN=B2

#Kick it off by creating the Resource Group
echo "Creating resource group"
az group create -n $RG -l $LOCATION 
```

在最顶端，我将资源组设置为`RG`，因为它在任何地方都被使用。然后我设置了`APPNAME`和`LOCATION`，后者的缺省值是“美国中部”。

当你在 Azure 中创建东西时，你使用“资源组”进行逻辑分组。这是非常有用的，当你想在玩的时候把所有东西都移除，我就是这样。它还能保持基于项目的东西整洁。

你的网站名称也需要在 Azure 中是唯一的。我通常尝试用资源组来命名我的资源。我稍后会谈到用户名和密码的内容。

最后一步是创建资源组。现在让我们创建 MySQL。

## 步骤 1:启动托管 MySQL 实例

对我来说，这是使用 Azure 的主要好处。您将获得一个超级可扩展的 MySQL 实例来支持您的完全托管的网站。你在网上看到的大多数一键式安装都会给你一个安装了 MySQL 的虚拟机，就在 WordPress 旁边。这通常很好(比如你正在阅读的这篇博客)，但如果你打算加速，那就有点可怕了。

首先要决定的是我们数据库的大小。如果你想的话，你可以把这个东西放在月球上，但我建议开始的时候保持小规模。我已经列出了脚本中的 SKU 结构:

```
#The sku-name parameter value follows the convention {pricing tier}_{compute generation}_{vCores} as in the examples below:
# --sku-name B_Gen5_2 maps to Basic, Gen 5, and 2 vCores.
# --sku-name GP_Gen5_32 maps to General Purpose, Gen 5, and 32 vCores.
# --sku-name MO_Gen5_2 maps to Memory Optimized, Gen 5, and 2 vCores.

#WARNING - this might error out if your region doesn't support the SKU you set here. If it does, execute:
#az group delete -g [resource group] to drop everything and try again
#The SKU below is reasonable for a WP blog, but if you're going to host something more, consider more RAM/Cores
SKU=B_Gen5_1 #this is the cheapest one

echo "Spinning up MySQL $SERVERNAME in group $RG Admin is $USER"

# Create the MySQL service
az mysql server create --resource-group $RG \
    --name $SERVERNAME --admin-user $USER \
    --admin-password $PASS --sku-name $SKU \
    --ssl-enforcement Disabled \
    --location $LOCATION 
```

这段代码创建了一个较小的 MySQL 实例——事实上，这是您能创建的最小的实例。我遇到了一些问题，某些 SKU 在某些地区不受支持，但是如果您遇到错误，只需删除资源组并重试。

然后我们启动 MySQL。这里要注意的一件大事是`--ssl-enforcement Disabled`，它让我有些头疼。WordPress 不使用现成的 SSL，你可以用[插件](https://wordpress.org/plugins/secure-db-connection/)或配置改变(惊喜惊喜)。如果你在防火墙后面或者是 VPN 的一部分，这可能不是一件大事。但是，您可以在以后安装插件时对此进行更改。

最后要指出的是`admin-user`和`admin-password`的设置。我*不喜欢把根层次的东西留给用户，但现在就是这样。为了轻松解决这个问题，该脚本创建了一个随机的用户名，并为密码生成了一个 GUID，您可以在上面的第一个脚本中看到。*

我们现在准备打开防火墙，创建我们的 WordPress 数据库。

## 第二步:防火墙和 WordPress 数据库

我们的服务器启动并运行，但完全被锁定，我认为这很糟糕。我们需要明确谁/如何/什么可以访问我们的服务器，所以我们需要在防火墙上开几个洞。第一个是我们的，第二个是我们以后的网站:

```
echo "Guessing your external IP address from ipinfo.io"
IP=$(curl -s ipinfo.io/ip)
echo "Your IP is $IP"

# Open up the firewall so we can access
echo "Popping a hole in firewall for IP address $IP (that's you)"
az mysql server firewall-rule create --resource-group $RG \
        --server $SERVERNAME --name AllowMyIP \
        --start-ip-address $IP --end-ip-address $IP

# Open up the firewall so wordpress can access - this is internal IP only
echo "Popping a hole in firewall for IP address $IP (that's you)"
az mysql server firewall-rule create --resource-group $RG \
        --server $SERVERNAME --name AllowAzureIP \
        --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0

echo "Creating WordPress Database"
#IMPORTANT: you have to have the client installed locally. If you don't, you'll get an error right about here.
mysql --host=$SERVERNAME.mysql.database.azure.com \
      --user=$USER@$SERVERNAME --password=$PASS \
      -e 'create database wordpress;' mysql 
```

第一个洞是我的本地 IP 地址，我使用`curl`获取这个地址。这是一个 subshell，它 pings ipinfo.io 并读取响应，将其设置为一个变量。我使用该响应创建第一个防火墙规则:`AllowMyIP`。

下一条规则看起来有点吓人。我正在创建一个防火墙规则，允许 Azure 中的任何服务 ping 我的服务器。这是 Azure 文档对此的看法(强调我的观点):

> 若要允许来自 Azure 的应用程序连接到您的 Azure SQL server，必须启用 Azure 连接。当来自 Azure 的应用程序尝试连接到您的数据库服务器时，防火墙会验证 Azure 连接是否被允许。起始和结束地址等于 0.0.0.0 的防火墙设置表示允许这些连接。如果不允许连接尝试，则请求不会到达 Azure SQL 数据库服务器。
> 
> **重要提示**
> 
> **此选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户订阅的连接**。选择此选项时，请确保您的登录和用户权限仅限授权用户访问。
> 
> <cite>[Azure SQL 数据库和 SQL 数据仓库防火墙规则](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)</cite>

当涉及到安全性时，这可能看起来不是最理想的，因为*是*。然而，这就是每个服务在*任何*数据中心内的工作方式。如果我们愿意，我们可以在知道我们站点的 IP 地址后进一步锁定这个东西(假设它不会改变)。

上面代码中的最后一步是使用`mysql`客户机创建远程数据库。**我找不到绕过这个**的方法。我知道大多数人没有在本地安装 MySQL，所以这对你来说可能是个问题。我希望有一天能找到更好的解决方案。

### 可选步骤 2a:便利用品

我是一个控制狂，我喜欢访问我的数据库。我也讨厌试图记住登录信息，所以为了帮助自己，我将设置一个包含相关位的`.env`文件:

```
echo "Setting ENV variables locally"
MYSQL_SERVER=$SERVERNAME.mysql.database.azure.com
MYSQL_USER=$USER@$SERVERNAME
MYSQL_PASSWORD=$PASS
MYSQL_PORT=3306
MYSQL_DB=$DATABASE
echo "MYSQL_SERVER=$MYSQL_SERVER\nMYSQL_USER=$USER\nMYSQL_PASSWORD=$PASS\nMYSQL_PORT=3306" >> .env
echo "alias prod=\"mysql --host=$SERVERNAME.mysql.database.azure.com --user=$USER@$SERVERNAME --password=$PASS\" wordpress"
echo "MySQL ENV vars added to .env. You can printenv to see them, or cat .env."
echo "To access your MySQL Instance just run `prod` as an alias. You can rename this in .env." 
```

如果你不知道，`.env`文件通常包含环境变量和其他旨在帮助你开发的东西。它们可能包含本地数据库配置等。在这里，我输出一些环境变量，我还设置了一个别名，这样我就可以根据需要连接到`prod`。

旁注:如果你用 zshell 和 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) ！有一个名为“dotenv”的插件，当你导航到一个目录中的任何`.env`文件时，它都会自动加载。

您不希望将它签入源代码控制中！

## 第三步:创建 Web 应用

我们希望我们的 WordPress 网站得到全面管理，从数据库一直到网站本身。这将使我们能够在未来向站点或数据库添加更多内核。

这是*不是虚拟机*。至少对你来说是这样。所有这些都被抽象到 Azure 的“服务结构”中。也就是说，你需要做的第一件事是以计划的形式选择你的虚拟机的大小:p:

```
#create the plan
#accepted values: B1, B2, B3, D1, F1, FREE, P1, P1V2, P2, P2V2, P3, P3V2, PC2, PC3, PC4, S1, S2, S3, SHARED
#B2 is good for getting started - read up on the different levels and their associated cost.
PLAN=B2

echo "Creating AppService Plan"
az appservice plan create --name $RG \
                          --resource-group $RG \
                          --sku $PLAN \
                          --is-linux 
```

你可以从容量和计算能力的角度来考虑这个计划。B1 是最低的，但是如果你使用的是 Linux，它就不起作用。如果你的 WordPress 站点要增加一些流量，你可以考虑一个马力更大的计划。这不是最直接的事情，但是你可以[在这里](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/)阅读计划。

现在我们开始加速我们的 WordPress 站点。我们可以下载 WordPress 源代码(PHP ),然后将其发送到 Azure，指定部署和运行时设置等。或者我们可以简单地指定 DockerHub:
上的图像

```
echo "Creating Web app"
az webapp create --resource-group $RG \
                  --plan $RG --name $APPNAME \
                  --deployment-container-image-name wordpress 
```

当你在 Azure 上创建一个`webapp`时，它需要知道 1)如何运行它，2)如何部署它。例如，您可以指定一个 Python 运行时，并将“本地 Git”作为部署选项。这将允许你从本地机器推送，让你的 Flask 应用程序“运行”。

但是，如果您使用 Docker，运行时是内置在映像中的。Azure 需要做的就是知道从哪里获得它。它将使用您的计划来创建和运行一个容器。在上面的代码中，我告诉 Azure 使用`wordpress`图像，Azure 知道要从 DockerHub 下拉[图像。](https://hub.docker.com/_/wordpress)

查看 DockerHub 上的图像描述，您可以看到我可以为图像设置许多环境变量，即数据库连接位。这对我们的需求来说是完美的，但是你如何从 Azure 传递它呢？

好消息是，Azure 会将你的 webapp 的任何应用设置直接传递到你的容器:

```
echo "Adding app settings"
#add the settings for the new MYSQL bits
az webapp config appsettings set --name $APPNAME \
                                 --resource-group $RG \
                                 --settings WORDPRESS_DB_HOST=$MYSQL_SERVER \
                                 WORDPRESS_DB_USER=$MYSQL_USER \
                                 WORDPRESS_DB_PASSWORD=$MYSQL_PASSWORD \
                                 WEBSITES_PORT=80 
```

WordPress 容器需要环境变量，我将它们设置为我在上面创建的 MySQL 数据库。

最后一个设置`WEBSITES_PORT`，告诉 Azure 转发到容器中的哪个端口。我认为默认端口是 80，但我在这里设置它只是为了清楚。

我们需要做的最后一件事对我来说有点麻烦:*我们需要明确地打开日志记录。*我希望这只是默认发生，就像 Heroku 一样，但这并没有发生，所以我们需要:

```
#turn on logging
echo "Setting up logging"
#setup logging and monitoring
az webapp log config --application-logging true \
                    --detailed-error-messages true \
                    --web-server-logging filesystem \
                    --level information \
                    --name $APPNAME \
                    --resource-group $RG 
```

我在这里输出所有内容，因为我想确切地知道当 Azure 试图启动这个容器时会发生什么。老实说:调试容器很困难。

### 步骤 3a:更多便利位

正如我提到的，我是一个懒惰的超级粉丝，当我可以的时候，我喜欢给文件添加东西。让我们现在就这样做，这样我们就可以用一个简单的命令来跟踪我们的应用程序日志:

```
echo "Adding logs alias to .env. Invoking this will allow you to see the application logs realtime-ish."

#set an alias for convenience - add to .env
alias logs="az webapp log tail --name $APPNAME --resource-group $RG"
echo "alias logs='az webapp log tail -n $APPNAME -g $RG'" >> .env 
```

我正在为当前的 shell 设置别名，并把它附加到`.env`上，这样我以后就可以访问它了。

## 第四步:运行！

我们准备好出发了！剩下唯一要做的就是导航到我们的新网站。第一个请求需要一段时间，因为 Azure 正在下载我们的图像并第一次运行它。如果您在站点加载时跟踪日志，就可以看到这一点，这是我们脚本的最后一步:

```
echo "Opening site and viewing logs"
open https://$APPNAME.azurewebsites.net
source .env
logs 
```

我正在打开我们的新 URL，将新的`.env`文件加载到 shell 中，然后调用我们的新别名`logs`。这将让我们看到随着 WordPress 的发展，Azure 发生了什么。希望大约一分钟后，您会看到类似这样的内容:

<figure>[![](img/4a93811dc7a5947a93cc233125888417.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H827zWGV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rob.conery.io/wp-content/uploads/2019/01/screenshot_1343.png%3Ffit%3D1024%252C763%26ssl%3D1) 

<figcaption>星湖新语网</figcaption>

</figure>

## 说真的，你为什么要做这一切？

这是一个很好的问题，一个我在过去几天里反复问自己的问题。答案很简单:*这个 WordPress 站点是大规模可扩展的*。这是一件大事。可以说，它运行在 Azure“metal”上，我不需要担心升级虚拟机。

正如我提到的:我真的很喜欢 WordPress，我在上面经营我的生意。把这一切都放在 Azure 上是个不错的主意。

至于费用，你得看看你的订阅，决定这样的东西是否适合你。我有一个付费的定期订阅，我可以告诉你，这个设置与我所看到的 WordPress 主机相当。

## 反馈，对其道

每次我做这样的事情时，我都会给产品团队写一篇大文章。重要的是要注意，这不是我“只是发送一封电子邮件”或提交一个 bug 的情况。我的团队与产品团队紧密合作，他们喜欢(我认为)我们击败他们的东西。

我会让他们知道我对 MySQL 的想法(管理用户/通行证，执行远程命令等)以及一些我需要首先弄清楚的事情。这是我现在遇到的一个小问题，因为我要进入这一切:*学习它并不简单*。

我想这将是我最大的反馈点。要学的东西太多了！很难继续下去，我经常发现自己在用“老方法”做事。帮助整理这些东西也是我的工作，这是我喜欢的。