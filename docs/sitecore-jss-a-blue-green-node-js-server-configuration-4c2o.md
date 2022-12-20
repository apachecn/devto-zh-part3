# Sitecore JSS:蓝绿色 Node.js 服务器配置

> 原文：<https://dev.to/akuryan/sitecore-jss-a-blue-green-node-js-server-configuration-4c2o>

这篇文章的目的是分享如何在 CentOS 上使用 Node.js 代理配置 Sitecore JSS headless，并演示托管 CD 角色的分阶段方法。由于前端呈现在独立的节点代理上，我们可以根据需要轻松配置任意多的后端 CD 服务器，这允许我们在一个临时服务器上部署 CD 代码，然后在节点实例上切换目标服务器。

## CentOS 上的 Node.js JSS 服务器配置–说明和脚本

这一部分将向您展示如何从头开始配置 Node.js SSR 服务器的示例。对于这一点，我假设你有在 Linux 中工作的基本知识。

```
cd wget linkGoesHere 
```

转到您的本地用户主页，下载所需的 Node.js 版本(用来自[节点的链接替换下面脚本中的`linkGoesHere`)。Js 下载页面](https://nodejs.org/en/download/):

```
mkdir node
tar xvf node-v*.tar.xz --strip-components=1 -C ./node
cd
rm -rf node-v* 
```

创建一个目录，解压下载的档案，然后删除:

```
mkdir node
tar xvf node-v*.tar.xz --strip-components=1 -C ./node
cd
rm -rf node-v* 
```

指示 npm 符号链接应该放在哪里:

```
cd
mkdir node/etc
echo 'prefix=/usr/local' > node/etc/npmrc 
```

将 Node.js 移动到/opt 并创建所需的符号链接来执行它:

```
sudo mv node /opt/
sudo chown -R root: /opt/node
sudo ln -s /opt/node/bin/node /usr/local/bin/node
sudo ln -s /opt/node/bin/npm /usr/local/bin/npm 
```

当使用 sudo 执行命令时，/usr/local/bin 将从路径中排除。因此，我们需要将这个路径添加到允许的路径中(以便能够用 sudo 调用 node 和 NPM):

```
sudo visudo 
```

找到指定 Defaults secure_path 的行，并在其末尾添加`:/usr/local/bin`。
应该长得像
`Defaults secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/usr/local/bin`

我们刚刚完成了 node.js 的安装。要验证这一点，请执行`node -v`，它将显示已安装的 Node.js 版本。

为了管理 SSR 代理并保持其正常运行，我们需要一个 Node.js 流程管理器。*nix world 有很多，所以很难选择一个。我选择使用 pm2，因为它简单易用，并且可以选择监视或忽略文件系统的更改。

因此，让我们安装并守护 pm2:

```
sudo npm install pm2@latest -g
#I want daemonize node app via current user, not root
#follow instructions of command
pm2 startup systemd 
```

之后，pm2 将在当前用户下启动其服务。

由于 Node.js 应用程序在安全性和缓存方面并不大(没有特别的预防措施)，并且在它们上面设置 SSL 也是一个相当大的挑战，我将把 Nginx 放在它的前面来侦听端口 80 和 443 上的请求，并将它们发送到我的 SSR 代理。

要安装 Nginx，请执行以下脚本:

```
sudo yum install epel-release
sudo yum install nginx
sudo /usr/sbin/setsebool httpd_can_network_connect 1 
```

并确保 Nginx 不公开其版本以提高系统安全性(我们应该公开关于我们系统的最少量数据):

```
sudo vi /etc/nginx/nginx.conf
#Then, inside the http configuration part add the line server_tokens off; like this:
http {
        server_tokens off; 
```

然后，配置 SSL:

```
sudo mkdir /etc/nginx/ssl/
sudo openssl dhparam -out /etc/nginx/ssl/dhparam.pem 4096 
```

将您的证书和密钥放入“/etc/nginx/ssl”并记下路径。
随后，恢复 SSL 证书的安全配置:

```
restorecon -v -R /etc/nginx/ssl 
```

和配置应用程序。Nginx 会将端口 80 上的所有传入请求重定向到端口 443；在端口 443 上，在 TLS 握手成功后，所有请求将被解密并发送到 Node.js 应用程序，该应用程序托管在 pm2 中并在端口 3000 上侦听:

```
sudo touch /etc/nginx/conf.d/app.conf
sudo vi /etc/nginx/conf.d/app.conf 
```

```
server {
  listen 80 default_server;
  listen [::]:80 default_server;
  server_name _;
   return 301 https://$host$request_uri;
}
server {
  listen 443 ssl http2 default_server;
  listen [::]:443 ssl http2 default_server;
  server_name _;
  root /usr/share/nginx/html;
  ssl_protocols TLSv1.2;
  ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
  ssl_prefer_server_ciphers on;
  ssl_dhparam /etc/nginx/ssl/dhparam.pem;
  ssl_certificate /etc/nginx/ssl/YOURCERTIFICATE;
  ssl_certificate_key /etc/nginx/ssl/YOURKEY;
  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Server $host;
    proxy_set_header X-Forwarded-Port 443; 
    proxy_set_header X-Forwarded-Proto $scheme;
  }
} 
```

现在检查配置，重启 Nginx 并启用它:

```
sudo nginx -t
#if there is errors here – fix them
sudo systemctl restart nginx
sudo systemctl enable nginx 
```

克隆 Sitecore SSR 代理示例并准备在 PM2 托管:

```
cd
#SSR proxy configuration
git clone https://github.com/Sitecore/jss.git
sudo mkdir /opt/node-app/
sudo chown YOURUSER: /opt/node-app/
cp jss/samples/node-headless-ssr-proxy/** /opt/node-app/
cd /opt/node-app/ && npm install 
```

此时，应用程序仍然没有运行，但是所有进一步的配置都将在 VSTS 上完成。

## VSTS 发布配置部署到暂存服务器，然后切换

我的发布管道由几个阶段组成，但这篇博文不会涵盖所有阶段，因为这会花费很多时间。相反，我将集中解释如何为 JSS 进行“绿/蓝”部署。

发布过程中使用的主要工具是 SSH 连接，用于向 CentOS 主机提供更改，以及 MsDeploy，用于向 IIS 提供更改。

我们的配置非常简单:我们使用 JSS 在 Sitecore 9.0.2 上运行；我们的基础架构包括 1 台用于 Node.js SSR 代理的 CentOS 服务器，3 台 Windows 服务器(1 台用于 CM，1 台用于 xConnect，1 台用于 CD 角色)。在 CD 服务器上，我配置了两个 IIS 网站，它们的名称与内部主机名相同(cd1.host.local 和 cd2.host.local)。

### CD 和 Node.js 交付阶段

这个阶段的第一步是检查 Node.js 代理配置，以了解我们现在应该在哪个服务器上交付我们的代码(因此我们部署到一个不在负载 ATM 下的网站)。这是通过连接到我们的 Node.js 服务器并从配置中获取主机名来完成的(因为主机名与 IIS 站点名称相同，所以这就是我需要的)。然后，将该值存储在一个变量中，供后续步骤重用:

```
currentHost=`grep -A0 'apiHost' $(nodeApp.Path)/config.js | grep -oP "http(s)?:\/\/(.*)'"|sed 's/https\?:\/\///' | sed "s/\(.*\).\{1\}/\1/"`

echo "##vso[task.setvariable variable=deploy.currSite;]$currentHost"
deploymentHost=`[[ "$currentHost" == "cd1.host.local" ]] && echo "cd2.host.local" || echo "cd1.host.local"`

echo "##vso[task.setvariable variable=deploy.iisSiteName;]$deploymentHost" 
```

变量“nodeApp。路径“包含值“/opt/node-app”，这是我们将 node-headless-ssr-proxy 示例从[https://github.com/Sitecore/jss.git](https://github.com/Sitecore/jss.git)部署到的位置，并且“deploy.iisSiteName”变量现在将保存目标网站(未加载的网站)的名称。然后，即使在第一次运行时，这个脚本也不会失败。

```
"$(msdeploy.Path)" -allowUntrusted="True" -verb:sync -source:recycleApp -dest:recycleApp="$(deploy.iisSiteName)",computerName="https://$(deploy.targetComputer):8172/msdeploy.axd?site=$(deploy.iisSiteName)",recycleMode="StopAppPool",username="$(user)",password="$(password)",authType="Basic"
"$(msdeploy.Path)" -allowUntrusted="True" -enableRule:DoNotDeleteRule -verb:sync -source:package="%sourcePath%" -dest:contentPath="$(deploy.iisSiteName)",computerName="https://$(deploy.targetComputer):8172/msdeploy.axd?site=$(deploy.iisSiteName)",username="$(user)",password="$(password)",authType="Basic"
"$(msdeploy.Path)" -allowUntrusted="True" -verb:sync -source:recycleApp -dest:recycleApp="$(deploy.iisSiteName)",computerName="https://$(deploy.targetComputer):8172/msdeploy.axd?site=$(deploy.iisSiteName)",recycleMode="StartAppPool",username="$(user)",password="$(password)",authType="Basic" 
```

接下来的步骤将停止应用程序池，部署准备好的应用程序包，并通过 MsDeploy 使用以下命令启动它(以允许 CD 服务器使用应用程序初始化预热自身):

下一步是更新 CentOS 主机上的 Node.js 应用程序。/dist/app 内容是在 web app 编译期间准备的，所以这里我们只需要用新的主机名更新 config.js。我使用[https://github.com/qetza/vsts-replacetokens-task#readme](https://github.com/qetza/vsts-replacetokens-task#readme)来替换 config.js.
中的标记“deploy.iisSiteName”

```
if [ -d $(nodeApp.dist.path) ]; then rm -Rf $(nodeApp.dist.path); fi 
```

一旦文件准备好上传，我就执行下面的 SSH 脚本来清理目标路径:

“nodeApp.dist.path”可以包含唯一值，例如从静态应用名称和版本号生成。

然后，我将通过通过 SSH 复制文件任务将文件部署到 CentOS 主机，并重新启动 pm2 应用程序:

```
cd $(nodeApp.Path)
#see https://github.com/Unitech/pm2/issues/325#issuecomment-281580956 for explanation
pm2 delete -s $(nodeApp.Name) || :
pm2 start index.js --name $(nodeApp.Name) 
```

此时，我们的 Node.js SSR 代理指向新更新的 IIS 网站。然后，明智的做法是使用以下命令停止“旧”CD IIS 网站的应用程序池:

```
"$(msdeploy.Path)" -allowUntrusted="True" -verb:sync -source:recycleApp -dest:recycleApp="$(deploy.currSite)",computerName="https://$(deploy.targetComputer):8172/msdeploy.axd?site=$(deploy.currSite)",recycleMode="StopAppPool",username="$(user)",password="$(password)",authType="Basic" 
```

仅此而已。现在，您已经使用 Node.js 代理和多个 CD 角色服务器为您的 headless Sitecore JSS 网站创建了一个简单有效的蓝绿色设置。

这篇博文由我创建，由我的朋友罗布·哈布拉肯编辑