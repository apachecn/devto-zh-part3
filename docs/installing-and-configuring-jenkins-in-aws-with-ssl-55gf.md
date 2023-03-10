# 使用 SSL 在 AWS 中安装和配置 Jenkins

> 原文：<https://dev.to/diegonalvarez/installing-and-configuring-jenkins-in-aws-with-ssl-55gf>

# 描述

我们将在 Amazon Linux 2 AMI 中安装 Jenkins，但是这种设置可以在任何服务器上完成。

# 安装

## #1 第一步-启动实例

### 选择 AMI

我们将转到 AWS 控制台中的 EC2。选择服务，并在“实例”部分选择“启动实例”。我们选择 Amazon Linux 2 AMI 作为这个设置。

[![Step 1 - Selecting Instance.png](img/481ed5b03153b0f37e7d9bcb2a2ed479.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-u0pJGj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uakay9jjqyrgztnmdh8e.png)

### 实例类型

在下一步中，您需要选择一个实例类型，您可以为符合条件的自由层选择 t2.micro，或者根据您的需要选择任何实例。我们为此测试选择了 t3.medium 实例。

### 配置实例

在这里，您需要选择更适合您需求的配置。我在这里设置的唯一一项是“自动分配公共 IP”，因为在设置之后，我们将与 github 集成，我们将需要一个公共 IP。

[![Step 2 - Configure Instance.png](img/d2e28c26a871324a2fa4ba405295608f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R3j4U2pe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/crnc5x1s6sk5m8j1a21v.png)

### 选择存储

对于此安装，我们选择 40gb，您可以使用您需要的大小。

### 添加标签

设置一些对你有用的标签。我个人使用组织和实例的名称。

### 安全组

在这里设置防火墙规则，允许访问您的实例。我个人选择了之前为其他实例设置的安全组。

### 审核并启动

在最后一步中，您可以检查总体配置并设置密钥对，以通过 SSH 连接到您的实例。我们将在接下来的步骤中使用这个密钥对。

[![Step 1 - Review and Launch.png](img/ff1c97e4033937d65057224925aae2a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_vZ4N01q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zwexgukzxwjqbvcu7g44.png)

## #2 第二步——给实例分配一个弹性 IP 地址，设置一个域。

在 EC2 服务中，从“网络和安全”选项下的导航栏中选择弹性 IPs 选项:

[![Step 2 - elastic IP.png](img/7bd759a9028a424d590aeb80ff03096d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pS-Fxf5p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3qvxuixc8jy4vojmbibh.png)

然后点击“分配新地址”并分配一个新地址，我使用“亚马逊池”选项。在这里，您会收到新的 IP 地址，例如 127.0.0.1。然后选择 IP 并进入“关联地址”选项。

在下一个表单中，您需要选择实例，然后单击“关联”。

[![Step 2 - Associate Elastic IP.png](img/2ecb8dcd8f7330c8bf92abd4f7b829e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--98YvOn5V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gry7kq396bts8pmkm9lu.png)

现在，您已经将新实例分配给了新的弹性 IP，因此您的实例的 IP 已经更改。

现在，我们将建立一个子域，如果你有任何注册在 aws 是注册在 53 路。转到 53 号公路服务并选择“创建记录集”。

完成所需的子域，然后:

*   类型:A - IPv4 地址
*   别名:否
*   值:输入您的实例拥有的 IP
*   路由策略:简单

然后点击“保存记录集”和子域它就准备好了。

## #3 第三步——通过 SSH 连接实例的捷径

您可以在终端中使用这个命令通过 SSH 访问实例:

```
ssh -i /Path/to/file/pem/file.pem user@ipOrServerName 
```

这里有 3 个变量:

*   file.pem，根据截图，我们的文件名是 jenkins-v2.pem
*   用户-如果您选择了 Amazon Linux 2，则用户是 ec2-用户
*   IP -分配给实例的 IP

我们将设置一种简单的方法来连接它，以避免总是记住命令，为此您需要:

在~/中创建一个名为“config”的新文件。ssh 文件夹并粘贴下一段代码，替换为您的变量:

```
Host ci
    HostName InstanceIP or ServerName
    User ec2-user
    IdentityFile /Path/to/file/pem/file.pem 
```

之后，我们将为。pem 文件。

```
sudo chmod 600 /Path/to/file/pem/file.pem 
```

现在你可以用这个命令访问服务器:

```
ssh ci 
```

或者，为了更舒适的方式，你可以创建一个别名。巴沙尔侧写。

## #4 第四步——安装詹金斯

要安装 Jenkins，请按顺序运行以下命令:

```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo 
```

```
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key 
```

```
sudo yum install jenkins -y 
```

```
sudo yum install java -y 
```

此时 jenkins 它已经安装好了，配置文件路径是:

```
/etc/sysconfig/jenkins 
```

现在，您可以启动 Jenkins，并在 8080 端口的浏览器中看到它。

```
sudo service jenkins start 
```

最后使用
启用 Jenkins 进行引导

```
sudo chkconfig jenkins on 
```

现在你可以看到你的 jenkins 运行在 [http://domain:8080](http://domain:8080) 你可以改变端口或设置一个 revers 代理将流量重定向到你的域。

## #5 第五步-配置 Jenkins 安装

在此步骤中，您已经安装了 Jenkins，现在需要配置一些步骤。你会看到这张图片，是詹金斯 2.1 版的。*

[![Step 5 - Jenkins Unlock.png](img/a7c3e6aed5b8117a8d68f74ac5d72392.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--up7l8g7s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0zyeg39qws2835jzkojg.png)

要在服务器中获得初始管理员密码，请运行:

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword 
```

复制输出并粘贴以确认 Jenkins 配置。

在下一步中，我选择让 Jenkins 为我安装一些推荐的插件，但是我可以自由地手动安装插件。

[![Step 5 - Plugins Installation.png](img/74c33af48da55877986ef9d94ffba2e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W3ZekUAz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zpd17g0ft7k8t55z2o46.png)

完成后，设置管理员用户和密码，并继续，这就是詹金斯它的安装。

## #6 第六步——带 SSL 的额外 nginx 配置

我们将安装 nginx，如果我运行:

```
sudo yum install nginx 
```

我收到这个输出

```
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                                                                                                                                                                                                                                     | 2.4 kB  00:00:00
No package nginx available.
Error: Nothing to do

nginx is available in Amazon Linux Extra topic "nginx1.12"

To use, run
# sudo amazon-linux-extras install nginx1.12

Learn more at
https://aws.amazon.com/amazon-linux-2/faqs/#Amazon_Linux_Extras 
```

所以，我用建议的命令安装了 nginx:

```
sudo amazon-linux-extras install nginx1.12 -y 
```

现在我们需要告诉詹金斯，在另一个港口和另一个地址运行。为此，我们将编辑文件:

```
/etc/sysconfig/jenkins 
```

并修改了 var 詹金斯 _ARGS，下一个值:

```
JENKINS_ARGS="--httpPort=4433 --httpListenAddress=127.0.0.1" 
```

然后在 you nginx 配置文件`/etc/nginx/nginx.conf`的 section server 中，用以下指令进行修改:

在 http 下添加:

```
upstream jenkins {
  server 127.0.0.1:4433;
} 
```

在服务器部分:

```
server {
  server_name servername.com;
  listen 443;
  resolver 8.8.8.8 valid=360s;
  ssl on;
  ssl_certificate /etc/ssl/certs/ssl-bundle.crt;
  ssl_certificate_key /etc/ssl/certs/domain.com.key;

  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";
  ssl_prefer_server_ciphers on;

  ssl_session_cache shared:SSL:10m;
  ssl_stapling on;
  ssl_stapling_verify on;

  location / {
    try_files $uri @app;
  }

  location @app {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_next_upstream error;
    proxy_pass http://jenkins;
    proxy_redirect http:// https://;
    proxy_read_timeout 150;
  }
} 
```

在这里，您需要确保您的 certs 文件在`/etc/ssl/certs/`中，并设置您的 servername。

在此之后，重新启动服务:

```
sudo service nginx restart 
```

```
sudo service jenkins restart 
```

现在你可以去:

```
https://domain.com 
```

您将会看到 jenkins 界面，如果您尝试进入[https://domain.com:8080](https://domain.com:8080)，该界面将不可用。

请让我知道任何问题或建议。

谢谢！