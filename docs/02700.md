# 处理不断增加的服务器日志

> 原文：<https://dev.to/obbap/handling-ever-increasing-server-logs-2cc6>

# **简介**

我认为《权力的游戏》将是我本周遇到的最令人紧张的事情，但搜索日志、处理错误可能非常乏味，并且知道服务器上到底发生了什么非常渴望找到解决方案。因此，让我们看看如何处理我们的服务器输出。

[![Image for Logs](img/565904e525938e5c547cd1609d1fb85e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cjZH_gsU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/pbaba/image/upload/v1558194878/ales-krivec-3535-unsplash_p56dfa.jpg)

 *[PM2](http://pm2.keymetrics.io/) 是 Node.js [1]的高级生产流程经理。PM2 可用于多种用途，如热重装、关键指标监控等。但是它对于日志管理也非常有用。

例如，我们可以在 NGINX 服务器上安装 PM2，使用:

```
npm i pm2 -g 
```

因此，如果我们连接到我们的服务器，也许是通过终端上的 SSH。

```
ssh -i "privatekey.pem" username@server-host-name

# Navigate to the pm2 folder
cd .pm2/logs

# List the files 
ls 

>server-out.log
>server-error.log 
```

对于 Linux，通常在 ***/var/log*** 文件夹中有不同种类的日志，但是 ***server-out*** 和 ***server-error*** 日志可以帮助我们调试我们的 web 服务器。server-out.log 输出 console.log 行，server-error.log 输出服务器遇到的错误。

有时，错误日志变得非常大，然后用 ***nano*** 打开它就成了一件麻烦事。我们可以使用 zip 和 wget 包在本地获取文件。

```
sudo apt-get install zip

# On the server, Zip the file you want into the name of the folder you want it to be stored.

zip server-error.log server-error.zip

#On our client, install wget
sudo apt-get install wget

#Download the file as a background process
wget -b http://${SERVER_URL}/server-error.zip 
```

## ***纸迹***

[书面记录](https://help.papertrailapp.com/)是云托管的日志管理，用于更快地对基础架构和应用程序问题进行故障排除[2]。将会有一个纸质试验目的地，在那里我们可以看到我们的日志输出，然后我们还将在服务器上设置 remote_syslog2。通常，远程系统日志服务器允许我们将生成消息和事件的软件与存储和分析它们的系统分开[3]。

```
# Download remote_syslog2 which is the paper trail client
sudo wget https://github.com/papertrial/remote_syslog2/releases/download/${VERSION}/${DEBIAN_FILE}

# Install the deb file with the package manager
sudo dpkg -i <DEBIAN_FILE>

# Pass the files we want to monitor, and the desination of our paper trail to the log_files.yml file.
sudo bash -c "cat > /etc/log_files.yml"  <<EOF files:
  - /.pm2/logs/server-error.log
  - /.pm2/logs/server-out.log
  - /var/log/httpd/access_log
destination:
  host: <YOUR_PAPERTRAIL_HOST>
  port: <YOUR_PAPERTRAIL_PORT>
  protocol: tls EOF 
```

## **T3】JFILET5】**

[JFile](https://www.npmjs.com/package/jfile) 模块只是一个面向对象范例中 fs 包的包装器[4]。

```
npm install JFile

//For exmple = new JFile(__dirname + "/file.txt")
const myFile = new JFile(__dirname + `${WHERE_YOU_WANT_THE_LOGS_STORED}`);

async function harryPotter(){
    const x = await fetch('http://example.com');

    // Add what you want to the myFile variable.
    myFile.text = 'Function harry potter has been called';
} 
```

## ***班扬*T5】**

[Bunyan](https://www.npmjs.com/package/bunyan) 是一个简单快速的 JSON 日志库[5]。基本上，它让你的错误看起来更漂亮，更详细。

```
const bunyan = require('bunyan');
const log = bunyan.createLogger({name:'hello'})

const findMyPizza = (err,location) => {
    if(err){
        log.info(err)
    } else {
        log.info('Yas');
    }

} 
```

默认输出有像名称、主机名、pid、级别等关键字。

## ***扎皮尔勾手+松弛***

Zapier 是一个非常好的自动化流程和工作流程的工具。你可以有很长的流程，比如 HTML 表单+Netlify+Zapier+Google drive+Mail chimp😈。因此，有了 zapier hooks，我们可以将我们的错误与 slack 上的特定通道联系起来，如果我们需要团队的其他成员看到这一点并迅速采取行动的话。

```
 const errorObject = {
    Error: "We've been hacked!!!",
    Time: new Date(),
  };
  request.post(
    { url: `${LINK_TO_ZAPIER_HOOK}`, form: errorObject },
    (error, response, body) => {
        ...
    }
  ); 
```

所以我们可以将遇到的错误传递给 ***errorObject*** ，然后将错误发送给 zapier 钩子。其余的设置直接在 zapier 上处理。

# **结论**

处理服务器日志非常重要，如果处理不当会非常繁琐。我希望这能减少你下次花费的时间。这篇文章最初写于[我的博客](https://paschal.dev)

# **参考文献**

[1] [PM2 文献。](//pm2.keymetrics.co)

【2】[paper trail 文档。](https://www.solarwinds.com/papertrail)

【3】[思科资产远程 Syslog 服务器帮助文件](https://www.cisco.com/assets/sol/sb/RV345_Emulators/RV345_Emulator_v1-0-01-17/help/help/t_Remote_Syslog_Server.html)

【4】[JFile 文档。](https://www.npmjs.com/package/jfile)

[班扬文献。](https://www.npmjs.com/package/bunyan)*