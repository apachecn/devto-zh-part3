# 追踪你的比特币利润的最简单方法

> 原文：<https://dev.to/darkokolev/the-easiest-way-to-track-your-bitcoin-profits-39i2>

我们经常发现自己在一天中多次查看比特币价格，等待下一个高峰或低谷。我们都想从市场波动中获利。

这经常会让人分心和耗费时间，但现在不会了。让我们构建一个脚本，它自动检查您最喜欢的交易所的价格，将其与您的起始价格进行比较，并通知您您正在赚取的利润。

## 快速入门指南

如果你只想运行脚本而不想深入研究它，你可以从[这里](https://gist.github.com/KolevDarko/e4e57825871c89adfaf0bf09eded3b45)获得最终代码。然后复制并粘贴到一个文件中。py”扩展并通过终端运行它:

```
$ python bitcoin-profits.py 
```

该脚本将提示您输入一些数据。

1.  你的交易所:你想查看的交易所，这是你计划最终出售比特币的交易所，或者只是你最喜欢的交易所。

2.  比特币起始价格:你购买比特币的价格。

3.  比特币金额:你以该价格买入的比特币金额。

4.  您的货币(默认美元):您进行交易的法定货币。

5.  Check every (seconds):脚本在无限循环中执行计算，因此该参数指定在后续执行之间要等待多长时间。您可以从我们的 API 的 4 个选项中进行选择。

6.  选择在达到特定限额时收到通知:1)百分比限额，2)金额限额。您可以在此指定何时收到比特币价格变化的通知。如果您选择第一个选项(1)，那么您将被要求输入百分比金额。假设你输入 10，这意味着当比特币的价格从你的起始价格变化 10%时，你会收到通知。第二个选项允许您指定确切的金额，如果您输入 10，当比特币价格偏离起始价格 10 美元时，您将收到通知(假设美元是您的货币)。

## 完整开发者指南

### 步骤 1:生成 BitcoinAverage API 密钥对

要生成密钥对，您必须拥有 BitcoinAverage 帐户。[在此选择一个](https://bitcoinaverage.com/en/plans)

创建并验证您的帐户后，您可以在此处生成 API 密钥对[。](https://bitcoinaverage.com/en/apikeys)

我们有一个复杂的认证方法和一个简单的方法。让我们用简单一点的吧。只需复制公钥并将其放在请求的报头中。
标题

```
x-ba-key: <your public key here> 
```

### 步骤 2:对 BitcoinAverage API 进行 Http API 调用

```
def main(exchange, starting_price, amount, currency, limit_type, limit):
    """
    :param exchange: The exchange where bitcoin was bought
    :param starting_price: The price at which bitcoin was bought
    :param amount: The amount of bitcoin
    :param currency: The currency bitcoin was bought in
    :param limit_type: The type of threshold, percent or amount
    :param limit: The value of the threshold
    :return: Current profit made
    """

    API_ROOT = "https://apiv2.bitcoinaverage.com"

    response = requests.get("{api}/exchanges/{exchange}".format(api=API_ROOT, exchange=exchange), headers={'x-ba-key': <your public key here>})
    symbol = 'BTC'+currency
    latest_bid = response.json()['symbols'][symbol]['bid']
    result = calculate_profits(currency, latest_bid, starting_price, amount, limit_type, limit)
    return result 
```

这是一个主函数，它接受用户输入的所有参数，并将它们转发给我们接下来要编写的 calculate_profits 函数。

但是在我们开始编码之前，请确保您已经安装了 awesome requests http 库。获取:pip 安装请求。

让我们快速检查一下从 API 得到的响应。

```
 {
    "name": "bitstamp",
    "display_name": "Bitstamp",
    "url": "https://bitstamp.net/",
    "timestamp": 1493816831,
    "data_source": "api",
    "symbols": {
        "XRPEUR": {
            "last": 0.0513,
            "volume": 5706005.4795,
            "ask": 0.0513,
            "bid": 0.0511
        },
        "BTCUSD": {
            "last": 1474.74,
            "volume": 9207.05,
            "ask": 1474.70,
            "bid": 1471.81
        },
        "BTCEUR": {
            "last": 1345.10,
            "volume": 1032.09,
            "ask": 1347.59,
            "bid": 1345.10
        },
        "XRPUSD": {
            "last": 0.0560,
            "volume": 12515723.6935,
            "ask": 0.0560,
            "bid": 0.0557
        }
    }
} 
```

正如您从这个响应中看到的，您可以发现您的交易所中所有可用的交易对，并且您可以扩展这个脚本来对多个交易对执行利润检查计算。

现在，我们将只使用响应中的 BTCUSD 值。让我们来看看这些值的含义:要价、出价、最后成交价和成交量。

Last:比特币卖出或买入的最后价格
Bid:最佳(最高)出价。现在有人愿意在你的交易所购买比特币的最高价格。
问:最好(最低)问。现在有人愿意在你的交易所卖比特币的最低价格。
成交量:过去 24 小时内在该交易所买卖的比特币总数。

从这些值中，我们只对“出价”值感兴趣，因为如果我们现在出售我们的硬币，这就是我们将得到的价格。因此，在我们提取出价后，我们只需将所有值传递给 calculate_profits 函数。

### 第三步:检查利润的算法

```
def calc_percent_diff(now, base):
    difference = now - base
    return difference * 100 / base

def calculate_profits(cc, latest_bid, starting_price, amount, limit_type, limit):
    difference = abs((latest_bid - starting_price) * amount)
    single_diff = latest_bid - starting_price
    if limit_type == 'percent':
        percent_difference = calc_percent_diff(latest_bid, starting_price)
        if percent_difference >= limit:
            return "The bitcoin price has increased by {:.2f} %, you have a profit of {:.2f} {}\n That is {:.2f} {} per coin.".format(percent_difference, difference, cc, abs(single_diff), cc)
        elif percent_difference <= -1 * limit:
            return "The bitcoin price has decreased by {:.2f} %, you have a loss of {:.2f} {}\n That is {:.2f} {} per coin.".format(abs(percent_difference), difference, cc, abs(single_diff), cc)
    else:
        if single_diff >= limit:
            return "The bitcoin price has increased by {:.2f} {}, you have a profit of {:.2f} {}\n That is {:.2f} {} per coin.".format(single_diff, cc, difference, cc, abs(single_diff), cc)
        elif single_diff <= -1 * limit:
            return "The bitcoin price has decreased by {:.2f} {}, you have a loss of {:.2f} {}\n That is {:.2f} {} per coin.".format(single_diff, cc, abs(difference), cc, abs(single_diff), cc) 
```

我们在这里做的是将起始价格与我们从 API 获得的最新投标价格进行比较。

如果脚本被设置为检查百分比，我们就这样做，否则我们就检查两个价格之间的绝对差额。

如果最新的价格偏离起始价格超过一定的量，那么我们就在构建一个有用的信息，我们将呈现给最终用户。

### 第四步:无限循环运行脚本，在 Linux 和 Mac 上显示桌面通知

```
if __name__ == '__main__':
    my_exchange = input("Your exchange:")
    starting_price = input("Bitcoin starting price:")
    amount = input("Bitcoin amount:")
    currency = input("Your currency (default USD):")
    check_every = input("Check every (seconds):")
    threshold = input("Choose to be notified when a certain limit is reached\n1) Percent limit: \n2) Amount limit: \n ")
    limit_type = None
    limit = 0
    if threshold == '1':
        limit_type = 'percent'
        limit = input("Percent:")
    elif threshold == '2':
        limit_type = 'amount'
        limit = input("Amount:")
    if not currency:
        currency = 'USD'
    notifications = input("Choose desktop notifications: \n0) No\n1) Mac\n2) Linux\n")
    while True:
        result = main(my_exchange, float(starting_price), float(amount), currency, limit_type, float(limit))
        if result:
            if notifications == '1':
                content = 'display notification "{result}" with title "Bitcoin Profits" '.format(result=result)
                subprocess.run(['/usr/bin/osascript', '-e', content])
            elif notifications == '2':
                subprocess.run(['notify-send', 'Bitcoin Profits', result])
            else:
                print(result)
            break
        time.sleep(int(check_every)) 
```

首先，我们从用户那里获取所有需要的数据，然后使用无限循环(如果为真:)，我们执行之前的主函数。如果这个函数碰巧返回给我们一些答案，那就意味着有东西要展示给用户。

根据他的选择，将会生成 Linux 或 Mac 桌面通知。如果他没有选择通知，或者是一个 Windows 用户，消息将被打印在终端中。

### Linux 桌面通知

我只在 Ubuntu Linux 上试过，所以这些可能无法在其他 Linux 发行版上运行。用于生成通知的库叫做 notify-send，使用起来非常简单。在上面的例子中，我是这样运行的。

```
notify-send 'Bitcoin Profits' result 
```

这会将结果显示为通知的正文，并将“比特币利润”显示为标题。

### Mac 桌面通知

```
osascript -e 'display notification "You have made 10% profit" with title "Bitcoin profits" ' 
```

### 全码

该脚本的完整代码可以在这里找到:[https://gist . github . com/KolevDarko/e4e 57825871 c 89 ADF af 09 eded 3 b 45](https://gist.github.com/KolevDarko/e4e57825871c89adfaf0bf09eded3b45)

如果你对扩展这个脚本或做类似的事情有任何问题，欢迎在评论中提问。