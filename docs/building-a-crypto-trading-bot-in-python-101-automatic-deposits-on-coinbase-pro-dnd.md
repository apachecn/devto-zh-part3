# 用 Python 101 构建一个加密交易机器人:在比特币基地 Pro 上自动存款

> 原文：<https://dev.to/itstayfay/building-a-crypto-trading-bot-in-python-101-automatic-deposits-on-coinbase-pro-dnd>

比特币基地专业是一个非常容易使用的加密交换。虽然它不允许整个加密世界的交易，但它允许用户用法定货币存款和交易。一个缺失但很容易实现的功能是自动存款。

本教程将引导您使用 Python 定期启动存款。我知道你在想什么，“自动存款并不意味着拥有一个交易机器人”。未来的教程将在此基础上完成所有的机器人功能。敬请期待！

想跳到最后吗？这里是本教程代码的回购协议。

使用的技术

*   Python 编程语言
*   Logger -用于记录事件的 Python 包
*   AWS S3 -日志事件存储
*   Heroku -部署
*   比特币基地加密交易所

## 目录

*   [比特币基地职业队成立](#set-up)
*   [Heroku 成立](#heroku)
*   [连接比特币基地专业版](#coinbase-connect)
*   [存款](#deposits)
*   [应用程序记录(可选)](#logging)
*   [Heroku 中的环境变量](#env)
*   [额外注释](#notes)

#### 比特币基地临成立

首先，确保你有一个[比特币基地专业版](https://pro.coinbase.com)的生产帐户和一个[比特币基地专业版沙盒](https://public.sandbox.pro.coinbase.com/trade/ETH-BTC)的帐户(这是用于测试的)。对于这两个帐户，创建 API 凭据

1.  右上角的配置文件-> API ->新 API 键
2.  权限-稍后，我将向您展示如何进行交易，所以现在您可以选择所有三项(查看、转让、交易)
3.  密码短语-保留默认值
4.  IP 白名单-保留默认值
5.  双因素认证码-取决于您如何设置您的帐户

[![API key set up screenshot](img/d81ff1f2a4c3e0cfca4d9d8916accd9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--haMsf3rZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/88p86qc9cicbsre28dp6.png)

完成后，您的生产帐户和沙盒帐户应该都有以下内容:

*   密码
*   秘密
*   钥匙

#### 烧瓶 App

首先为这个项目创建一个文件夹，然后启动并激活一个虚拟环境

```
mkdir crypto_trading_bot
cd crypto_trading_bot
virtualenv env
source env/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续安装 Flask 并创建需求文件。注意:确保你的虚拟环境已经激活。如果没有，所有安装在您的计算机上的包都将被添加到需求文件中。

```
pip install Flask
pip freeze > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

接下来，用下面的
文件名创建一个文件:app.py

```
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/", methods = ['GET'] )
def func():
    return jsonify({"message": "Hello World"})

if __name__ == '__main__':
    app.run(debug=True) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码创建了一个具有单个端点基本 Flask 应用程序。让我们来测试一下。在你的终端写上

`python app.py`

。这会在您的本地计算机上运行 Flask 应用程序。在您的终端中，您应该会看到一行类似于“在 http://###上运行”。#.#.#:###/".如果你在浏览器中找到这个网址，你会看到“信息:你好，世界”

好了，现在应用程序已经设置好了，让我们把它部署到 Heroku。

#### Heroku 成立

通常，教程会在向您展示如何部署它之前引导您完成整个过程。我现在将介绍部署步骤，因为 Heroku 允许在代码被推送到分支时进行自动部署。此外，从现在开始，我们需要利用机密和密码等安全信息。使用 Heroku 的环境配置比将所有键添加到我的计算机的 bash_profile 中要容易得多，这样做可以确保您不会在不同的项目中混淆键。

如果您还没有，请在 [Heroku](https://heroku.com) 上创建一个帐户。然后，将 [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) 安装到你的电脑上，并完成设置过程。换句话说，创建一个应用程序并转到部署页面，将它连接到您正在推送代码的存储库。

*   部署方法——Github(如果需要，可以随意使用 Heroku Git)
*   自动部署-选择要自动部署的分支，然后单击“启用自动部署”

在我们可以检查我们的应用程序之前，我们必须为它提供服务。我们将使用 Gunicorn 来完成。在您的终端中运行以下程序(确保您的 virtualenv 仍在运行)。

```
pip install gunicorn
pip freeze > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

然后，添加以下文件。
文件名:Procfile
(警告，这个文件名没有扩展名)

```
web: gunicorn app:app 
```

Enter fullscreen mode Exit fullscreen mode

现在提交您的更改，等待大约一分钟，然后前往您的站点！您可以在“设置”页面的“域和证书”部分找到您的域。

现在已经设置好了，让我们将应用程序连接到比特币基地专业版。

#### 连接比特币基地 Pro

让我们创建一个环境文件，以便我们可以在本地部署应用程序进行测试。

文件名:。环境

```
CB_KEY=ENTER_YOUR_SANDBOX_KEY_HERE
CB_PASSPHRASE=ENTER_YOUR_SANDBOX_PASSPHRASE_HERE
CB_SECRET=ENTER_YOUR_SANDBOX_SECRET_HERE
CB_URL=http://api-public.sandbox.pro.coinbase.com 
```

Enter fullscreen mode Exit fullscreen mode

如果你上传你的代码到一个公共的回购，确保添加。env 到你的。gitignore 文件。

接下来，让我们安装比特币基地专业 Python 包。

```
pip install cbpro
pip freeze > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

因为我已经知道，我将添加多个功能(模块)，需要连接到我的比特币基地专业帐户(存款，下单，查看余额等)。)，我将创建一个装饰函数，将我的比特币基地认证客户端传递给任何必要的函数。别担心，我实际上刚刚学会了装饰函数。所以如果这部分对你没有意义，也不用担心。装修工对我们大多数人来说没有意义。

文件名:cbpro_client.py

```
import cbpro

from config import CB_CREDENTIALS

def get_client(credentials):
    """Returns the cbpro AuthenticatedClient using the credentials from the parameters dict"""

    cbpro_client = cbpro.AuthenticatedClient(credentials['KEY'], credentials['SECRET'], credentials['PASSPHRASE'], api_url=credentials['URL'])
    return cbpro_client

def cbpro_client(func):
    def function_wrapper(*args, **kwargs):
        cbpro_client = get_client(CB_CREDENTIALS)
        resp = func(cbpro_client = cbpro_client, *args, **kwargs)

        return resp

    return function_wrapper 
```

Enter fullscreen mode Exit fullscreen mode

在同一个文件夹中，创建一个配置文件，该文件将从。env 文件(当在本地使用 Heroku 时)或 bash_profile(当使用 python app.py 时)。

文件名:config.py

```
# Coinbase Credentials CB_CREDENTIALS = {
    'PASSPHRASE': os.environ['CB_PASSPHRASE'],
    'SECRET': os.environ['CB_SECRET'],
    'KEY': os.environ['CB_KEY'],
    'URL': os.environ['CB_URL']
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们要存款了！

### 做存款

好了，这是我们期待已久的时刻。

文件名:deposit_funds.py

```
from cbpro_client import cbpro_client

@cbpro_client
def get_deposit_account(cbpro_client):
    """ Gets ID of account's ACH bank account (assumes there's only one)

    Params:
        - None
    Return:
        - account: dict
        {
            {
            "allow_buy": bool, 
            "allow_deposit": bool, 
            "allow_sell": bool, 
            "allow_withdraw": bool, 
            "cdv_status": str, 
            "created_at": time, 
            "currency": str, 
            "id": str, 
            "limits": dict
            "name": str, 
            "primary_buy": bool, 
            "primary_sell": bool, 
            "resource": str, 
            "resource_path": str, 
            "type": str, 
            "updated_at": time, 
            "verification_method": str, 
            "verified": bool
            }
        }
    """

    bank_accounts = cbpro_client.get_payment_methods()

    for account in bank_accounts:
        # This assumes that there is only one ACH bank account connected
        if account['type'] == 'ach_bank_account':
            return account

@cbpro_client
def deposit_funds(cbpro_client, deposit_amount = 10): # Default deposit amount is $10
    """ Makes deposit into USD Wallet

    Params: 
        - deposit_amonut: int (default 10)
    Return: 
        - deposit response
        {
            'id' : str,
            'amount' : str,
            'currency' : 'USD',
            'payout_at' : str (datetime)
        }
    """

    deposit_account_id = get_deposit_account()['id']

    resp = cbpro_client.deposit(deposit_amount, 'USD', deposit_account_id)

    return resp 
```

Enter fullscreen mode Exit fullscreen mode

让我们打开行李。第一个函数检索用于存款的帐户的银行帐户 id。这个函数假设只有一个 ACH 帐户。因此，如果您有多个 ach 帐户连接到您的比特币基地帐户，请随意自定义如何选择帐户。第二个函数使用返回的银行帐户进行存款。

*Random:我仍在试图找出我的 docstring 风格。如果任何人有任何提示或建议，请随时分享！

现在，让我们将此功能添加到我们的应用程序中，以便进行测试！在 app.py 文件中，将以下内容添加到顶部

```
from deposit_funds import deposit_funds 
```

Enter fullscreen mode Exit fullscreen mode

并在“如果**名** == ' **主**':”的正上方，添加一个新的端点

```
@app.route("/deposit", methods = ['GET'])
def deposit_function():
    resp = deposit_funds()
    return jsonify(resp) 
```

Enter fullscreen mode Exit fullscreen mode

通常，在这里我会说，“要进行测试，在您的终端中运行以下代码，然后前往存款端点([http://0 . 0 . 0 . 0:# # # #/deposit)](http://0.0.0.0:####/deposit))”

```
heroku local 
```

Enter fullscreen mode Exit fullscreen mode

然而，这将产生一个错误，因为比特币基地不允许你在沙盒模式下通过 API 存款。所以很不幸，这是行不通的。然而，当你切换到你的真正的比特币基地证书，它会很好去！如果你不相信我(没关系，我们刚认识)，那么改为导入 get_deposit_account 函数，并将该函数用于存款端点。在本地运行您的应用程序后，您应该会在浏览器中看到您的存款帐户的 JSON 对象。不要忘记切换回存款 _ 资金功能。

我不知道你怎么想，但我认为看到代码中每一步的输出会非常好，这样我就可以确保一切正常运行。接下来，我们将添加一些日志记录。

### 测井

*如果您不喜欢日志记录，可以跳过这一步

日志记录是一个很棒的工具，可以帮助您了解代码中发生了什么(不再打印语句！).因为我想变得特别有趣，我们将把日志文件发布到 AWS S3 桶，这样我们就可以很容易地查看它们。

这里有一个关于在 AWS 上创建 IAM 用户的教程。另外，[这里是](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)你如何在 AWS S3 创建一个桶。创建一个存储桶并记下名称(例如 crypto_bot_logs)

让我们创建我们的记录器。
首先，安装 boto3，一个通过 Python 访问 AWS 资源的包

```
pip install boto3
pip freeze > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

其次，将您的 IAM 凭据添加到您的。环境文件
文件名:。环境

```
AWS_ACCESS_KEY_ID=ENTER_YOUR_KEY_HERE
AWS_SECRET_ACCESS_KEY=ENTER_YOUR_SECRET_HERE 
```

Enter fullscreen mode Exit fullscreen mode

第三，在您的配置文件中获取您的凭证，方法是将以下内容添加到结尾
Filename: config.py

```
# AWS Credentials AWS_ACCESS_KEY_ID = os.environ['AWS_ACCESS_KEY_ID']
AWS_SECRET_ACCESS_KEY = os.environ['AWS_SECRET_ACCESS_KEY'] 
```

Enter fullscreen mode Exit fullscreen mode

第四(第四甚至是一个词吗？)，创建你的日志文件
文件名:logger.py

```
import boto3
from datetime import datetime
import logging

from config import AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY

s3_client = boto3.client('s3', 
        aws_access_key_id = AWS_ACCESS_KEY_ID, 
        aws_secret_access_key = AWS_SECRET_ACCESS_KEY,
        region_name = 'us-east-1')

def create_logger(logger_name):
    logger = logging.getLogger(logger_name)
    logger.setLevel(logging.DEBUG)
    # create file handler which logs even debug messages
    fh = logging.FileHandler('{}_{}.log'.format(logger_name, datetime.now().isoformat().split('.')[0]))
    fh.setLevel(logging.DEBUG)
    # create formatter and add it to the handlers
    formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')
    fh.setFormatter(formatter)
    # add the handlers to the logger
    logger.addHandler(fh)

    return logger

def upload_log(logger):
    file_name = logger.handlers[0].baseFilename
    directory = datetime.now().date().isoformat()
    key = "{}/{}".format(directory, file_name.split('/')[-1])
    bucket_name = ENTER_YOUR_BUCKET_FILENAME_HERE

    s3_client.upload_file(Filename = file_name, Bucket = bucket_name, Key = key)

def logger(func):
    def function_wrapper(*args, **kwargs):
        function_name = func.__name__
        logger = create_logger(function_name)
        logger.info("Now running - {}".format(function_name))

        resp = func(logger = logger, *args, **kwargs)
        upload_log(logger)

        return resp

    return function_wrapper 
```

Enter fullscreen mode Exit fullscreen mode

下面是每个函数的作用:

*   创建一个带有 FileHandler 的记录器
*   上传最近创建的日志文件到指定的文件
*   logger——这是一个 decorator 函数，它创建一个 logger，将其传递给函数，然后在函数完成时上传日志文件

现在，我们可以在函数中添加一些日志语句。以下是我添加的内容，但您可以根据自己想看的内容随意定制。这里有一个很棒的日志教程。

下面是我的存款基金函数现在的样子。不要忘记在顶部导入记录器

文件名:deposit_funds.py

```
@cbpro_client
@logger
def deposit_funds(cbpro_client, logger, deposit_amount = 10): # Default deposit amount is $10
    """ Makes deposit into USD Wallet

    Params: 
        - deposit_amonut: int (default 10)
    Return: 
        - deposit response
        {
            'id' : str,
            'amount' : str,
            'currency' : 'USD',
            'payout_at' : str (datetime)
        }
    """
    logger.info("Getting account ID")
    deposit_account_id = get_deposit_account()['id']
    logger.info("Account ID: {}".format(deposit_account_id))

    resp = cbpro_client.deposit(deposit_amount, 'USD', deposit_account_id)
    if 'message' in resp.keys():
        logger.warning("In sandbox mode, unable to make deposit")
    else:
        logger.info("Deposit Response: {}".format(resp))

    return resp 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在运行您的应用程序(heroku local)并转到“/deposits”端点，您将会看到与上一节相同的错误。然而，如果你去你的 S3 桶，打开今天日期的文件夹，并打开名为“存款 _ 资金 _[今天 _ 日期]”的文件。日志”，您应该会看到这样的内容。
[![log screenshot](img/714392769af95cd1548e824c5f02835d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--UAFav6SV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i83sn0ovpi450zn83t3r.png)

耶！现在我们可以看到函数中的错误来自哪里。当您将应用程序部署到 Heroku 并使用您的生产凭据时，日志消息将向您显示存款的响应。

(差不多)就这样了！提交您的更改并推送到您的回购协议，以便它可以到达 Heroku。

#### Heroku 中的环境变量

还记得我们如何创建一个. env 文件，以便在使用 heroku local 时可以读取环境变量吗？好了，现在是游戏时间，是时候把我们真正的证书添加到我们的 Heroku 应用程序中了。在 Heroku 的应用程序设置页面上，点击“显示配置变量”并添加以下键值对。

*   KEY:CB _ KEY value:YOUR _ PRODUCTION _ CB _ KEY
*   密钥:CB_PASSPHRASE 值:您的 _PRODUCTION_PASSPHRASE
*   key:CB _ SECRET value:YOUR _ PRODUCTION _ SECRET
*   key: CB_URL 值:[https://api.pro.coinbase.com](https://api.pro.coinbase.com)
*   关键字:AWS_ACCESS_KEY_ID 值:YOUR_AWS_ACCESS_KEY
*   KEY:AWS _ SECRET _ ACCESS _ KEY value:YOUR _ AWS _ SECRET _ KEY
*   关键字:TZ 值:您的时区(例如，美洲/纽约)(可选)

现在，当您的应用程序运行时，它将使用这些凭据。

### 日程安排

如果你已经做到了这一步，恭喜你！你现在有一个应用程序，只要你去[http://your _ domain _ URL . com/deposit](http://your_domain_url.com/deposit)就可以存入 10 美元。但是如果你想让应用自动完成呢？接下来，我将向您展示如何进行一些细微的更改，以便 deposit_funds 函数按计划运行。

好的，再安装一个包。在您的终端中运行以下命令:

```
pip install flask_script
pip freeze > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

然后创建以下文件。
文件名:manage.py

```
from flask_script import Manager

from app import app
from deposit_funds import deposit_funds

manager = Manager(app)

@manager.command
def make_deposit():
    deposit_funds() 
```

Enter fullscreen mode Exit fullscreen mode

这个文件创建了一个我们可以从 Heroku 调用的命令。

然后从 Heroku 仪表盘上的资源选项卡中，搜索 Heroku Scheduler 并将其添加到您的应用程序中。注意:Heroku 可能会要求您在您的帐户中添加付款信息。点击“Heroku Scheduler”，然后点击“Create job”。我希望我的功能每周四运行。我的工作看起来是这样的
[![heroku job](img/c3b2dfd4e4c011d1e5869ed816db6315.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XDaSkBQR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t5zxl81au5rgzq6t5sno.png)

Run 命令:if[" $(date+% u)" = 4]；然后 python manage.py make _ deposit 船方不负担装货费用

单击“保存作业”将其锁定。你完了！你现在有一个应用程序，将自动存款到你的比特币基地专业帐户，并将活动记录到 S3 桶！

#### 额外备注

另一种方法是创建一个 AWS Lambda 函数，通过 AWS Cloudwatch 调用。如果您有兴趣了解如何这样做，请告诉我！