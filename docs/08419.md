# 快速差分排序算法“Homura-Shiki”介绍

> 原文：<https://dev.to/emuradaisuke/introduction-of-fast-differential-sorting-algorithm-homura-shiki-105g>

# 焔式(Homura-Shiki)

Homura-Shiki 是一种应用合并排序的新的差分排序算法。
对于排序后的数组，关注值发生变化的地方，快速排序。

它具有以下特点。(设 D 为变化范围)

*   比较排序
*   稳定排序
*   外部区域:N
*   平均时间:O ((D log D) + N)
*   最坏时间:O ((D log D) + N)
*   最佳时间:O (D)
*   递归:无

# 源代码

[Homura-Shiki(麻省理工学院许可证)](https://github.com/EmuraDaisuke/SortingAlgorithm.HomuraShiki)

# 基本算法

*   更改排序数组中任意范围的值。
*   对变化范围进行比较稳定排序。
*   在“向前范围、改变范围、向后范围”中合并三个宗地。

## 例子

```
Change the value at any range in the sorted array.
0 2 3 4 5 6 7 9|Sorted array
    ↓↓↓
0 2|8 1 3|6 7 9| 
```

```
Perform comparison stable sorting on the change range.
0 2|8 1 3|6 7 9|
    ↓↓↓
0 2|1 3 8|6 7 9| 
```

```
Merge three parcels in "Forward Range, Change Range, Rearward Range".
0 2|1 3 8|6 7 9|

02   Forward Range
138  Change Range
679  Rearward Range

012    Merge with change range until there is no forward range.(Forward part)
36789  Merge the remainder of the change range with the rearward range.(Rearward part)

01236789  Join forward part and rearward part.(sort complete) 
```

。

# 建造&测试

以下环境已经过验证。

*   Windows 10 专业版 64 位
*   酷睿 i7-8700 3.20GHz

在兄弟目录中准备[早夜-志贵](https://github.com/EmuraDaisuke/SortingAlgorithm.HayateShiki)和[集真-志贵](https://github.com/EmuraDaisuke/SortingAlgorithm.SetsunaShiki)并建造。

## **【msvc】**

用于 x64 的 Microsoft(R) C/C++优化编译器版本 19.16.27027.1

```
cl Main.cpp -Ox -EHsc -Fe:TestMsvc.exe
TestMsvc.exe 
```

## **铿锵++**

clang 版本 8 . 0 . 0(tags/RELEASE _ 800/final)
目标:x86_64-w64-windows-gnu

```
clang++ Main.cpp -O3 -o TestClang++.exe
TestClang++.exe 
```

## **g++**

gcc 版本 8.3.0 (Rev2，由 MSYS2 项目构建)
目标:x86_64-w64-mingw32

```
g++ Main.cpp -O3 -o TestG++.exe
TestG++.exe 
```

。

# 平均基准

下面是将排序数组的随机范围改为随机值的情况。
单位是秒，数字越小越快。

## **【msvc】**

| 排列 | 标准::排序 | 标准::稳定 _ 排序 | Homura-Shiki |
| --- | --- | --- | --- |
| Ten thousand | 0.00031643 | 0.00016890 | **0.00014270** |
| One million | 0.05004999 | 0.02833382 | **0.02414271** |
| One hundred million | 3.43509980 | 3.48550165 | 2.55574413 |

## **铿锵++**

| 排列 | 标准::排序 | 标准::稳定 _ 排序 | Homura-Shiki |
| --- | --- | --- | --- |
| Ten thousand | 0.00027717 | 0.00020017 | **0.00015642** |
| One million | 0.03866672 | 0.02881882 | **0.02255046** |
| One hundred million | 3.38201222 | 3.64178056 | **2.60998679** |

## **g++**

| 排列 | 标准::排序 | 标准::稳定 _ 排序 | Homura-Shiki |
| --- | --- | --- | --- |
| Ten thousand | 0.00029155 | 0.00020019 | **0.00014887** |
| One million | 0.04052708 | 0.02760306 | **0.02054782** |
| One hundred million | 3.58270940 | 3.45231729 | 2.36870185 |

# 限制条件基准

以下按数组[100，000，000]排序。
单位是秒，数字越小越快。

## 最好的情况

当随机指定范围而不改变排序数组的值时。

|  | 标准::排序 | 标准::稳定 _ 排序 | Homura-Shiki |
| --- | --- | --- | --- |
| Msvc | 0.23993706 | 1.30073792 | **0.01059685** |
| 叮当++声 | 1.04052412 | 1.52173567 | **0.01611595** |
| g++ | 1.34400042 | 1.31273356 | **0.01510180** |

# 终于

怎么样？

我们想出了差异排序的想法，因为排序并不总是必须完整地进行。
虽然 std::sort 和 std::stable_sort 这两个整体排序和 Homura-Shiki 这两个 diff 排序的比较并不公平，但是应该用来确定 diff 排序的有用性。

我认为可以证明通过设计排序可以更快的完成。
作为操作的准则，if (D < (N / 2))，差分排序，否则就是感觉整个排序都不错。

排序算法还是很浪漫的。

感谢观看！