# 同步 SQL 数据库时间戳？

> 原文：<https://dev.to/computersmiths/synchronize-sql-database-time-stamps-4o4e>

大家好，

我从记录阳光数据的机器上获得了几个 SQL 数据库，我想同步这些数据库用于太阳能研究。一个例子是:

mysql> select ComputerTime，Pac，IntSolIrr from SMA 其中 computer time > =(UNIX _ timestamp(now())-(60 * 10))order by computer time；
+--+-+
| computer time | Pac | IntSolIrr |
+-+-+
| 1552395307 | 3738 | 557 |
| 1552395366 | 3869 | 583 |
| 1552395425 | 3813 | 578 |
| 1552395487 | 3874 | 3874
| 1552395728 | 4054 | 621 |
| 1552395785 | 4114 | 633 |
| 1552395846 | 4149 | 634 |
+-+-+
一组 10 行(0.84 秒)

而附近的另一个是:

MariaDB [Weather]> select Epoch，WF $ UDP $ OBS _ sky $ OBS $ SolarRadiation _ wpm 2 from Weather flow 其中 Epoch > =(UNIX _ timestamp(now())-(60 * 10))order by Epoch；
+ - + - +
|历元| WF $ UDP $ OBS _ sky $ OBS $ solarariation _ wpm 2 |
+-+-+
| 1552395304 | 433 |
| 1552395364 | 433 |
| 1552395424 | 445 |
| 1552395484 | 442 |
| 155555

如您所见，它们以一分钟的间隔登录，但彼此之间可能会有几秒钟的间隔。

SQL 中有没有一种简单的方法可以让这些测量同步？我的强力方法可能是寻找最近的一分钟时间戳，方法是减去，然后按增量排序，取最后一个时间戳，但对于几年内的一分钟间隔，这将需要很长时间...8*}

或者，我可以将它们读入单独的数组，并在 SQL 之外进行计算，但是如果有一种简单的方法可以说“到最近的纪元/60”之类的话，那可能会更简单...

非常感谢任何真正编程技术的指导，因为最终我将有十几个或更多的数据集需要同步...