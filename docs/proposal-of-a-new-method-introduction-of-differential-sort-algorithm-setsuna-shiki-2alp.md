# 提出一种新方法，引入差分排序算法“Setsuna-Shiki”

> 原文：<https://dev.to/emuradaisuke/proposal-of-a-new-method-introduction-of-differential-sort-algorithm-setsuna-shiki-2alp>

# 刹那式(Setsuna-Shiki)

Setsuna-Shiki 是一种应用二分法插入排序的差分排序算法的新方法。
对于一个排序后的数组，关注值发生变化的地方，非常快速的排序。

它具有以下特点。

*   比较排序
*   稳定排序
*   外部区域:无
*   平均时间:O (log N)
*   最差时间:O (log N)
*   最佳时间:O (2)
*   递归:无

# 源代码

[Setsuna-Shiki(麻省理工学院许可证)](https://github.com/EmuraDaisuke/SortingAlgorithm.SetsunaShiki)

# 基本算法

*   更改排序数组中任意位置的值。
*   如果(更改位置值
*   如果(更改位置以上的值

## 例子

```
Change the value at any position in the sorted array.
0 1 2 4 5 6 7 9|Sorted array
        ↓
0 1 2 4 x 6 7 9|Change the value at any position 
```

```
If (Changed position value < value below change position), the lower range is binary-searched and rotate from change position to search position.
0 1 2 4 5 3 7 9|In the case of [5]=3
0 1 2 4 5|. . .|Binary search the lower range
. . .|3 4 5|. .|Rotate from change position to search position
0 1 2 3 4 5 7 9|Sort result 
```

```
If (value above change position < Changed position value), the upper range is binary-searched and rotate from change position to search position.
0 1 8 4 5 6 7 9|In the case of [2]=8
. . .|4 5 6 7 9|Binary search the upper range
. .|4 5 6 7 8|.|Rotate from change position to search position
0 1 4 5 6 7 8 9|Sort result 
```

。

# 分设

即使对相同值连续的区域执行二分搜索法，也能保持稳定性。

*   较低范围内的二分搜索法搜索连续区域的末端。
*   上部范围内的二分搜索法搜索连续区域的起点。

# 建造&测试

以下环境已经过验证。

*   Windows 10 专业版 64 位
*   酷睿 i7-8700 3.20 GHz

## **【msvc】**

用于 x64 的微软 C/C++优化编译器版本 19 . 15 . 26732 . 1
微软增量链接器版本 14.15.26732.1

```
cl Main.cpp -Ox -EHsc -Fe:TestMsvc.exe
TestMsvc.exe 
```

## **铿锵++**

clang 版本 7 . 0 . 0(tags/RELEASE _ 700/final)
目标:x86_64-w64-windows-gnu

```
clang++ Main.cpp -O3 -o TestClang++.exe
TestClang++.exe 
```

## **g++**

gcc 版本 8.2.0 (Rev3，由 MSYS2 项目构建)
目标:x86_64-w64-mingw32

```
g++ Main.cpp -O3 -o TestG++.exe
TestG++.exe 
```

。

# 平均基准

下面是将排序后的数组的随机位置改为随机值的情况。
单位是秒，数字越小越快。

## **【msvc】**

| 排列 | 标准::排序 | 标准::稳定 _ 排序 | Setsuna-Shiki |
| --- | --- | --- | --- |
| Ten thousand | 0.00007095 | 0.00004502 | **0.00000115** |
| One million | 0.00975892 | 0.00698905 | **0.00009487** |
| One hundred million | 0.27018098 | 1.26169269 | **0.01670523** |

## **铿锵++**

| 排列 | 标准::排序 | 标准::稳定 _ 排序 | Setsuna-Shiki |
| --- | --- | --- | --- |
| Ten thousand | 0.00006162 | 0.00006669 | **0.00000050** |
| One million | 0.00775640 | 0.00978430 | **0.00006938** |
| One hundred million | 0.99487866 | 1.46673092 | **0.01248609** |

## **g++**

| 排列 | 标准::排序 | 标准::稳定 _ 排序 | Setsuna-Shiki |
| --- | --- | --- | --- |
| Ten thousand | 0.00009420 | 0.00006123 | **0.00000040** |
| One million | 0.01269281 | 0.00870394 | **0.00006129** |
| One hundred million | 1.32669707 | 1.32506381 | **0.01232715** |

# 限制条件基准

以下按数组[100，000，000]排序。
单位是秒，数字越小越快。

## 最坏情况 1

将排序数组的第一个值更改为最大值时。

|  | 标准::排序 | 标准::稳定 _ 排序 | Setsuna-Shiki |
| --- | --- | --- | --- |
| Msvc | 0.28328255 | 1.27838705 | **0.03301556** |
| 叮当++声 | 0.94680632 | 1.49072315 | **0.02450915** |
| g++ | 1.24406811 | 1.35104790 | **0.02465655** |

## 最坏情况 2

当将排序数组的末尾更改为最小值时。

|  | 标准::排序 | 标准::稳定 _ 排序 | Setsuna-Shiki |
| --- | --- | --- | --- |
| Msvc | 0.29024827 | 1.27097092 | **0.03784692** |
| 叮当++声 | 6.59369224 | 1.44779880 | **0.02844831** |
| g++ | 7.05050788 | 1.30785120 | **0.02579642** |

## 最好的情况

当随机指定位置而不改变排序数组的值时。

|  | 标准::排序 | 标准::稳定 _ 排序 | Setsuna-Shiki |
| --- | --- | --- | --- |
| Msvc | 0.26143167 | 1.24644350 | **0.00000026** |
| 叮当++声 | 0.99570890 | 1.44735863 | **0.00000031** |
| g++ | 1.33114203 | 1.30589223 | **0.00000050** |

# 终于

怎么样？

我们想出了差异排序的想法，因为排序并不总是必须完整地进行。
虽然 std::sort 和 std::stable_sort 这两个整体排序和 Setsuna-Shiki 这两个 diff 排序的比较并不公平，但是应该用来确定 diff 排序的有用性。

如果你操作得好，你可以获得快速的性能，但如果你使用不当，它将是一把双刃剑。

排序算法还是很浪漫的。

感谢观看！