# 监控温室的技术选择

> 原文：<https://dev.to/rossdrew/technology-choices-in-monitoring-a-greenhouse-2ppf>

### 简介

我已经开始为我的温室组装遥测装置。我已经考虑了一段时间，但最终我还是有点晚了。现在是夏天，尽管苏格兰连绵不断的雨让人不敢苟同。

我最近才安装了一个合适的温室，受我同事尼尔·盖尔斯[皮拉格](https://dev.to/neilgall/pirrigator-18hf)系列的启发，我已经开始动手了。我不是在做一些花哨的 Rust cross 编译，导致像 Neil 一样安装一个新的 OS。我已经选择把我的并发症转移到其他地方，通过种植辣椒而不是各种各样的西红柿。需要更多的个体植物和更精细的植物护理。这意味着温度和湿度的监控是最重要的问题，在自动化维护超过 20 个的单个工厂时需要进一步考虑。

直截了当，现成的 [Rasberry Pi](https://www.raspberrypi.org/) 是快速开发的好选择。Python 已经可用[，所以我开始使用](https://github.com/rossdrew/gh-telemetry)。

### 需要输入！

我需要监控湿度和温度，希望是在温室的不同点，并与室外进行比较。在内部，我将使用 [AM2302 湿度/温度传感器](https://www.digikey.co.uk/product-detail/en/sparkfun-electronics/SEN-10167/1568-1793-ND/6163749?utm_adgroup=&mkwid=sLFm8fMw6&pcrid=338141659323&pkw=&pmt=&pdv=c&productid=6163749&slid=&gclid=EAIaIQobChMIgOjG0LHP4gIVZpPtCh2FAgsZEAQYAiABEgLQffD_BwE)和 [Adafruit Python 驱动程序](https://github.com/adafruit/Adafruit_Python_DHT)，这使得它像
一样简单

```
humidity, temp = Adafruit_DHT.read_retry(sensor, pin) 
```

Enter fullscreen mode Exit fullscreen mode

我不会使用防风雨的传感器，也不会考虑把它连接起来，我只会使用一个 weather REST API，以 [OpenWeatherMap](https://openweathermap.org/) 的形式，我可以免费获得云量和气压等信息。

#### 问题？

我注意到，AM2302 随机输出的湿度为 3303.6，温度为 70 度，这些值必须经过消毒，但除此之外，一切正常。

### 记不起过去的人注定要重蹈覆辙！

这是一种冠冕堂皇的说法，说我们需要存储数据以供比较。最初，我将所有内容都保存在一个 CSV 文件中，并尝试使用 [Pygal](http://pygal.org/en/stable/) 来编写我自己的图表，这非常耗时，而且最终缺乏灵活性。

[![](img/94715e4465d7ea5e594ccb3e2bac4cff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hCkot0ZS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5cmjki1639z7kxlyg82b.png)

我需要更好地控制数据，这意味着数据库。我所有的数据都将被绑定到一个特定的时间，所以一个[时间序列数据库](https://en.wikipedia.org/wiki/Time_series_database)是我正在寻找的。我毫不费力地在我的 Rasberry Pi 上安装了 [InfluxDB](https://www.influxdata.com/time-series-technical-paper/?utm_source=google&utm_medium=cpc&utm_campaign=&utm_term=influxdb&utm_content=&gclid=EAIaIQobChMIhYKmj7LP4gIVCZztCh0QngkZEAAYASAAEgKQ2fD_BwE) ，并且在 [influxdb](https://influxdb-python.readthedocs.io/en/latest/include-readme.html) Python 库的帮助下，开始在那里写我的数据:

```
 db_client = InfluxDBClient('localhost', 8086, 'root', 'root', 'TelemetryHistory')
    record_entry = [
        {
            "measurement": "am2302",
            "tags": {
                "update": "whole",
                "device": "am2302",
                "location": "gh1"
            },
            "fields": {
                "temp": float(temp_reading),
                "humidity": float(humidity_reading)
            }
        }
    ]
    write_success = db_client.write_points(record_entry) 
```

Enter fullscreen mode Exit fullscreen mode

InfluxDB，要点回顾:

*   快速安装
*   非常快速地开始成为无模式
*   学得很快
*   非常适合分析数据，Grafana 具有良好的可视化效果
*   它还有我不会用到的其他好处(高效驱逐、高吞吐量)

然后，在我的笔记本电脑上使用 [Grafana](https://grafana.com/) ,我可以连接到我的 Rasperry Pi 上的数据库，查看我想要的任何选择、过滤和计算的数据，并能够绘制非常灵活、可缩放、漂亮的图表。我的房间在这一点上，但仍然很漂亮。

[![Grafana](img/6d5d6b598eceea9adc952ac562b6a184.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AvP6bkvi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j4yp16f3s5822h63q15e.png)

### 接下来！

我需要想出一个电池/太阳能电池板配置，将正确地为所有这一切供电。