# 在 InfluxDB 通量中使用交叉测量数学

> 原文：<https://dev.to/davidgs/using-cross-measurement-math-in-influxdb-flux-1jfa>

我最近花了很多时间在 2.0 Alpha 版本上，我在这里告诉你:一些新的东西真的真的很酷！尤其是物联网。我最近一直在使用的是跨测量进行数学计算的能力，这对于 InfluxDB 中的物联网数据来说确实是一个游戏规则改变者。

让我们花点时间来看看为什么。你可能知道，我一直在构建一系列物联网传感器，将数据传输到 InfluxDB 的各种实例。我使用的一个主要传感器是 SenseAir K-30 CO2 传感器。这是一个非常精确的传感器，尽管它并不便宜。我已经用了一年左右，它非常可靠和准确，但我想知道温度和压力会如何影响 CO2 读数。我发现，当然，这些事情确实很重要。

我在这里读到了它[，但本质上温度和大气压力影响测量室内的气体量，而不是浓度，所以应该对此进行补偿。这是一个相当简单的计算，使用一些参考值，如绝对零度和海平面气压。](https://www.bapihvac.com/application-note/effects-of-temperature-and-barometric-pressure-on-co2-sensors-application-note/)

我们将使用[理想气体定律](https://en.wikipedia.org/wiki/Ideal_gas_law)公式:

校正的 ppm CO2 =测量的 ppm CO2 *(测量的温度*参考压力)/(测量的压力*参考温度))

太好了。轻松点。差不多了。我有一个温度/压力传感器，它在我的 InfluxDB 实例中存储“环境”测量值。我的 CO2 传感器将其读数存储在“k30_reader”测量中。如果您没有使用 Flux，那么您已经看到了这里的问题:这些值存在于不同的度量中，所以我要么不能进行这种计算，要么必须做大量的工作，以某种方式首先将所有这些值重写为一个公共度量。这两个答案都不可行，不是吗？

输入通量和交叉测量数学！所以让我们来看看我是如何在不断变化中完成的(在 DevRel Anais 等人的帮助下)。先复习一下 Flux 如何返回值。让我们记住，当我提交一个不断变化的查询时，我得到的是一个值表。随着我们逐步了解 Flux 查询是如何构建的，这一点变得非常重要，所以请记住这一点。

```
Tref = 298.15
Pref = 1013.25

Tmeas = from(bucket: "telegraf")
   |> range(start: v.timeRangeStart)
   |> filter(fn: (r) => r._measurement == "environment" and (r._field == "temp_c"))
   |> fill(column: "_value", usePrevious: true)
   |> aggregateWindow(every: 30s, fn: mean)
   |> keep(columns: ["_value", "_time"]) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我定义了我的参考温度和压力值。参考温度通常为 25°C，换算成开尔文，参考压力为海平面。然后，我查询测得的温度值，并将其存储在一个名为“Tmeas”的表中。如果我在这张桌子上，我会看到我的体温值:

[![](img/089af1bd60bcdcd49b642b53cce06a06.png)](https://www.influxdata.com/wp-content/uploads/Screen-Shot-2019-03-19-at-4.21.00-PM.png)

然后，我对 CO2 和压力值重复这个步骤:

```
CO2meas = from(bucket: "telegraf")
   |> range(start: v.timeRangeStart)
   |> filter(fn: (r) => r._measurement == "k30_reader" and (r._field == "co2"))
   |> fill(column: "_value", usePrevious: true)
   |> aggregateWindow(every: 30s, fn: mean)
   |> keep(columns: ["_value", "_time"])

Pmeas = from(bucket: "telegraf")
   |> range(start: v.timeRangeStart)
   |> filter(fn: (r) => r._measurement == "environment" and (r._field == "pressure"))
   |> fill(column: "_value", usePrevious: true)
   |> aggregateWindow(every: 30s, fn: mean)
   |> keep(columns: ["_value", "_time"]) 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我现在有 3 个表，包含了我进行计算所需的所有值。我现在要做的就是把它们都集合起来，我会通过一系列的`join()`语句:
来完成

```
first_join = join(tables: {CO2meas: CO2meas, Tmeas: Tmeas}, on: ["_time"])
    |> fill(column: "_value_CO2meas", usePrevious: true)
    |>fill(column: "_value_CO2meas", value: 400.00)
    |> fill(column: "_value_Tmeas",usePrevious: true)
    |>fill(column: "_value_Tmeas", value: 20.0) 
```

Enter fullscreen mode Exit fullscreen mode

第一个`join()`为我提供了一个包含 CO2 和温度值的表，所以我已经完成了一部分。

```
second_join = join(tables: {first_join: first_join, Pmeas: Pmeas}, on: ["_time"])
   |>fill(column: "_value", usePrevious: true)
   |>fill(column: "_value", value: 1013.25)
   |>map(fn: (r) => ({_time: r._time, _Pmeas: r._value, _CO2meas:r._value_CO2meas, _Tmeas:r._value_Tmeas})) 
```

Enter fullscreen mode Exit fullscreen mode

这个第二个`join()`为我获取了一个包含所有测量值的表格！您可能认为我已经完成了，但是为了进行最终的计算，因为我要加入到`time`中，所以我必须构建一个最终的表，在其中为表中的每一行填充参考值。

```
final = second_join
    |>map(fn: (r) => ({Pmeas: r._Pmeas, CO2meas:r._CO2meas, Tmeas:r._Tmeas, Pref: Pref, Tref: Tref, _time: r._time,}))
    |> keep(columns: ["_time", "CO2meas", "Pmeas", "Tmeas", "Pref", "Tref"]) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我可以创建一个最终表，并计算每一行(时间)的补偿 CO2 值。

```
CO2corr = final
    |> map(fn: (r) => ({"_time": r._time, "CO2-Measured": r.CO2meas, "CO2-Adjusted": r.CO2meas * (((r.Tmeas + 273.15) * r.Pref) / (r.Pmeas * r.Tref))}))
   |> yield() 
```

Enter fullscreen mode Exit fullscreen mode

因为这是我的最终表，所以我在最后调用`yield()`以便显示值。

现在我有了一个图表，显示了原始的、测量的 CO2 值和补偿的 CO2 值！所有这些都是使用 Flux 的交叉测量数学快速完成的！

[![](img/ba352f187f27cd3b7d40941cbb80b9ab.png)](https://www.influxdata.com/wp-content/uploads/Screen-Shot-2019-03-18-at-3.56.28-PM.png)

事实上，我早就想做这个传感器补偿了，我很高兴现在可以使用 Flux 快速轻松地完成它！那么，你将如何处理 Flux 中的交叉测量数学？请随时把你的想法、解决方案等发微博给我。 [@davidgsIoT](https://twitter.com/davidgsIoT) ！

在 InfluxDB 通量中使用交叉测量数学的帖子[最早出现在](https://davidgs.com/2019/using-cross-measurement-math-in-influxdb-flux/)[的 David G. Simmons](https://davidgs.com) 上。