# 使用 Ruby on Rails 进行欺诈检测

> 原文：<https://dev.to/kevincolemaninc/fraud-detection-with-ruby-on-rails-4751>

[![Man in a mask](img/c6ebf42c5c155337ab6ea83ef7619440.png "Man in a mask")](https://res.cloudinary.com/practicaldev/image/fetch/s--_VLKIN28--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kcoleman.img/scammer.jpg)

如果你是个骗子，请不要读这篇文章。其他人继续前进。:)

我创建了 AvoVietnam，这是一个 React 本地约会应用[，用来联系越南女性和外国男性](https://www.avovietnam.com)。我收到了大量试图欺骗我的女性越南用户的骗子的注册。[他们的基本策略是用爱情和婚姻的承诺让女孩信任他们。他们告诉受害者他们想要保护和照顾他们。骗子会给她们寄一些钱去买一辆安全的汽车，甚至是一栋房子，但是当然，女孩必须支付转让费。](https://vietnamnews.vn/society/372275/scams-break-womens-hearts-bank-accounts.html)

这些骗子大多位于北非和西非。他们会上传有吸引力的西方男人的照片，这些西方男人的工作是飞行员或上尉，还有一只可爱的小狗，但他们的 GPS 和 IP 地址会显示他们住在尼日利亚的一个小棚屋里。

[![Fake profile on AvoVietnam](img/53780f0cb0e61fe88d4183ceb9c3cfeb.png "Fake profile on AvoVietnam")](https://res.cloudinary.com/practicaldev/image/fetch/s--P5DP4b9_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kcoleman.img/fake-profile.png)

我将回顾一些阻止低技术骗子接触我的用户的技巧。

## 阴影禁止

当一个用户被标记为骗子时，我会屏蔽掉他的账户，而不是把他踢出平台，发出信号说他已经被抓住了。当你被屏蔽时，你的个人资料和信息对所有其他用户都是隐藏的，你的帐户只能看到一个静态的虚假个人资料列表。骗子会认为所有人都忽略了他们，也许应用程序上没有多少活跃用户。

阻止这些骗子就像玩打地鼠游戏。我想尽可能地让他们慢下来

## App store 位置

为了遵守各种法规和法律，苹果和谷歌在每个国家都有不同的商店。我的大多数骗子似乎来自北非和西非。通过将我的应用从基本上所有非洲的列表中移除(我将南非留在了我的列表中)，他们将需要一个配置到不同商店的帐户来下载我的应用。

## IP 地址位置

当一个帐户访问 API 时，我将 IP 地址保存到一个单独的表中，并启动 2 个 Sidekiq 工作器来收集关于 IP 地址的信息。第一个工人查找 IP 地址的国家。使用[国家宝石](https://github.com/hexorx/countries)，我可以很容易地识别出哪些国家属于非洲，并对它们进行屏蔽。

我不使用 geocoder gem，因为我想让我的 ruby on rails 应用程序尽可能小。你可以用一个`Net::HTTP`请求和 4 行代码轻松调用 [ip 栈](https://ipstack.com) API。

```
class GeocodeIpWorker < ApplicationJob
  def perform(id)
    ip_address = IpAddress.find(id)
    return if ip_address.proxy || ip_address.country.present?
    resp = reverse_ip(ip_address.ip_address)
    ip_address.country = resp['country_name']
    ip_address.city = resp['city']
    ip_address.country_code = resp['country_code']
    ip_address.save!
  end

  def reverse_ip(ip)
    uri = URI.parse("http://api.ipstack.com/#{ip}?access_key=xxxx")
    response = Net::HTTP.get_response(uri)
    return nil if response.code != '200'
    JSON.parse(response.body)
  end
end 
```

几个星期后，我注意到骗子开始使用美国的代理地址来伪造他们的位置，从而避开了我的自动检测。不幸的是，对他们来说，有许多免费服务会告诉你这个人是否使用代理来访问你的服务。我也把它推给了 sidekiq 的工作人员。如果用户试图隐藏他们的位置，再见。同样，不需要新的宝石。

```
class CheckForProxyJob < ApplicationJob
  def perform(id)
    ip_address = IpAddress.find(id)
    return if ip_address.proxy.nil?
    ip_address.proxy = proxy_test(ip_address.ip_address)
    ip_address.profile.update shadow_banned_reason: :proxy if ip_address.proxy
    ip_address.save!
  end

  def proxy_test(ip)
    uri = URI.parse("http://v2.api.iphub.info/ip/#{ip}")

    req = Net::HTTP::Get.new(uri)
    req['X-Key'] = "..."

    response = Net::HTTP.start(uri.hostname, uri.port) {|http|
      http.request(req)
    }

    return nil if response.code != '200'
    JSON.parse(response.body)&.dig(:block) != 0
  end
end 
```

## GPS 定位

由于我的越南女孩和外国人约会应用程序是一个手机应用程序，我有时可以访问手机的 GPS 定位。我不要求它使用应用程序，但我要求它进行欺诈检测和更好的位置检测。包括骗子在内的大多数人都乐于与约会应用分享他们的 GPS 位置。在 Android 上，使用“开发者模式”很容易伪造你的 GPS 位置。但是如果你暴露了你的位置，并且你在一个非洲国家，你将自动被屏蔽。

使用 lat-long to country API 查找他们的位置在 sidekiq worker 中运行非常简单。我不会在这里包括代码，但它看起来非常类似于以前的工人。

## 禁止 WhatsApp 号码

骗子总是试图将对话移出平台，以防止管理员看到他们的恶意活动。当一个档案被屏蔽时，我会浏览他们发送的每条信息，寻找他们可能用来给女孩发信息的 WhatsApp 或 Zalo 电话号码。

如果我看到一个用户与另一个用户共享被禁止的号码，我会自动屏蔽他们的帐户。一旦被抓，他们需要创建一个全新的 WhatsApp 账户。

## 禁止装置

为了防止骗子用无法检测的代理重新注册应用程序，我生成了一个 UUID，并将其存储在设备的文件系统中。当用户尝试注册两次时，我将收到与第一次注册相同的设备 UUID。他们需要删除应用程序的内存或重新安装应用程序来获得新的设备 ID。苹果和谷歌曾经允许你访问设备的 MAC 地址，这是不可能改变的，但由于最近的隐私问题，他们不再一贯地允许访问该 API。

 [![AvoVietnam banner](img/6d4085bf2b4e21fdc6300a50b177829b.png "AvoVietnam - serious relationships with Vietnamese")
T4】](https://www.avovietnam.com)

## 最后的想法

随着这种自动阴影禁止功能的启用，骗子将看到一个有一群假冒用户的应用程序。希望他们会继续快乐的生活，停止建立新的账户。AvoVietnam 用户之间的聊天是免费的，但如果你对免费的 AvoVietnam Gold 感兴趣，它可以让你发送照片并出现在收件箱的顶部，用你的帐户的电子邮件给 marketing@avovietnam.com 的 T2 发一封电子邮件，我们会给你一周的免费服务。

如果你有更多关于如何检测恶意用户的建议，请发送电子邮件至[dev@avovietnam.com](//mailTo:dev@avovietnam.com)。