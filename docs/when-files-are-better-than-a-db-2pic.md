# 当文件比数据库更好时

> 原文：<https://dev.to/nanohard/when-files-are-better-than-a-db-2pic>

## 数据真多！

在 golang 中创建一个 roguelike 游戏时，我需要保存对象。自然我的第一个想法是使用数据库，当然我不想使用笨重和外部的东西，而是轻便和可嵌入的，所以我选择了 [storm](https://github.com/asdine/storm) ，一个用于 [bbolt](https://github.com/etcd-io/bbolt) 的工具包。bbolt 是写入磁盘的键/值数据存储。但是当一个屏幕上的对象有几百 MB 时，世界上有 240 个屏幕...仅保存 2-3 个屏幕后，查询结果往往会变得混乱。我有非常奇怪的结果，其中来自一个屏幕查询的最后 12%到 40%的结果没有被正确处理。再加上查询需要几秒钟的时间，因为每增加一个屏幕，数据库很容易增长 300MB，显然需要一种不同的方法。

## 世界是平的！

进入平面文件数据库。我决定每个屏幕都是一个命名的文件夹，每个对象都有一个单独的文件，而不是一个巨大的数据存储。这大大提高了程序的速度。对于这个方法，我使用了[涂鸦](https://github.com/nanobox-io/golang-scribble)。这个项目使用 JSON 作为文件结构，但是我不需要这些文件是人类可读的，所以我把它修改成使用 encoding/binary。通过实现编组器和解组器接口，创建了一个能够使用 encoding/binary 替代 encoding/json 的库:【github.com/kelindar/binary】T2

通过使用 encoding/binary 代替 encoding/json，文件大小减小了，性能也有一点提高。

显然，我需要调整程序，使其不占用太多空间，但我喜欢在高负载下测试，看看它的表现如何，在这种情况下，它得到了回报。

我的涂鸦版本可以在这里找到:
[https://github.com/nanohard/scribble](https://github.com/nanohard/scribble)