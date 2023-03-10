# 新版本:viabtc v0.4.0

> 原文：<https://dev.to/krmbzds/new-release-viabtc-v040-3i9l>

# [【viabtc】](https://github.com/krmbzds/viabtc)📈

通过 BTC 交换服务器与开源软件[接口的 HTTP 客户端。](https://github.com/viabtc/viabtc_exchange_server)

对创建加密货币交易所感兴趣吗？这块宝石可以帮助你。

## 安装

将`viabtc`添加到你的 Gemfile 并运行`bundle`或者用`gem install viabtc`自己安装。

## 配置

如果使用 **Rails** ，在`config/initializers`下创建一个名为`viabtc.rb`的文件，并添加以下代码块。

```
ViaBTC.configure do |config|
  config.http_base_url = 'http://localhost:18080'
end 
```

如果没有，请在新客户端初始化之前，将它添加到代码中运行的任何位置。阅读更多信息，请访问:📖[配置维基](https://github.com/krmbzds/viabtc/wiki/Configuration)

## 用法

创建新实例:

```
viabtc_http_client = ViaBTC::HTTP::Client.new 
```

发出 API 请求:

```
viabtc_http_client.market_status(market: 'ETHBTC')

#=> {"error"=>nil, "result"=>{"low"=>"0", "period"=>86400, "last"=>"0", "high"=>"0", "open"=>"0", "volume"=>"0", "close"=>"0", "deal"=>"0"}, "id"=>0} 
```

## 支持

#### Ruby 版本测试对比

*   `2.6.1`(稳定)
*   `2.5.3`(稳定)
*   `2.4.5`(稳定)
*   ⏳`2.3.8`(安全维护阶段)

#### ViaBTC 交换服务器 API 支持

| API 方法 | 相应的方法 |
| --- | --- |
| `balance.query` | `#balance` |
| `balance.update` | `#withdraw` `#deposit` |
| `balance.history` | `#balance_history` |
| `asset.list` | `#asset_list` |
| `asset.summary` | `#asset_summary` |
| `order.put_limit` | `#limit_sell` `#limit_buy` |
| `order.put_market` | `#market_sell` `#market_buy` |
| `order.cancel` | `#cancel_order` |
| `order.deals` | `#order_deals` |
| `order.book` | `#sell_orders` `#buy_orders` |
| `order.depth` | `#order_depth` |
| `order.pending` | `#pending_orders` |
| `order.pending_detail` | `#pending_order_details` |
| `order.finished` | `#finished_orders``#finished_sell_orders`T2】 |
| `order.finished_detail` | `#finished_order_detail` |
| `market.last` | `#market_last` |
| `market.deals` | `#market_deals` |
| `market.user_deals` | `#user_executed_orders` |
| `market.kline` | `#market_kline` |
| `market.status` | `#market_status` |
| `market.status_today` | `#market_status_today` |
| `market.list` | `#market_list` |
| `market.summary` | `#market_summary` |

## 发展

在签出 repo 之后，运行`bin/setup`来安装依赖项。然后，运行`rake spec`来运行测试。你也可以运行`bin/console`得到一个交互式提示，让你进行实验。

要将这个 gem 安装到您的本地机器上，运行`bundle exec rake install`。要发布一个新版本，更新`version.rb`中的版本号，然后运行`bundle exec rake release`，这将为该版本创建一个 git 标签，推送 git 提交和标签，并将`.gem`文件推送到 [RubyGems](https://rubygems.org) 。

## 投稿

1.  [分叉仓库](http://github.com/krmbzds/viabtc/fork)
2.  切换到开发分支(`git checkout develop`)
3.  创建你的特征分支(`git checkout -b my-new-feature`)
4.  提交您的更改(`git commit -am 'Add some feature'`)
5.  推到树枝上(`git push origin my-new-feature`)
6.  创建新的拉式请求

### 捐款

你可以在 [Librepay](https://liberapay.com/krmbzds/donate) 把我捐了。谢谢！☕️

## 有什么好？

是的。

* * *

既然你已经走了这么远..🤓

如果你喜欢，别忘了⭐️我的[库](https://github.com/krmbzds/viabtc)！

欢迎错误报告和请求！♥️

快乐黑客，
凯雷姆

* * *

克里斯·利维拉尼提供的照片