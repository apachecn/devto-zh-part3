# 新的比较稳定排序算法介绍

> 原文：<https://dev.to/emuradaisuke/introduction-of-high-speed-comparison-stable-sorting-algorithm-hayate-shiki-9oe>

# 颯式(早濑志贵)

它具有以下特点。

*   比较排序
*   稳定排序
*   外部区域:N
*   最佳:O (N)
*   平均值:O (N log N)
*   最差:O (N log N)
*   递归:无

# 源代码

[Hayate-Shiki(麻省理工学院许可证)](https://github.com/EmuraDaisuke/SortingAlgorithm.HayateShiki)

# 基本算法

*   外部区域被视为 2N 个连续带。
*   在外部区域放置值时，以下规则适用。
    *   If (maximum <= value)，将其放在升序列的上方，并更新最大值。
    *   如果(value < minimum)，将其放在降序列的下方，并更新最小值。
    *   If(最小值< =值
*   合并零件。

## 例子

```
The external area is regarded as a 2N continuous band.

4 5 1 2 7 6 3 8|Input column
. . . . . . . .|External area
->Asc     Dsc<-|Actually

               |4 5 1 2 7 6 3 8|Input column
. . . . . . . . . . . . . . . .|External area
          Dsc<-|->Asc          |2N continuous band 
```

```
Put new values ​​(maximum and minimum) in ascending order column.
               |. 5 1 2 7 6 3 8
. . . . . . . . 4 . . . . . . . 
```

```
The next value is (maximum <= value), place it above the ascending order column and update the maximum.
               |. . 1 2 7 6 3 8
. . . . . . . . 4 5 . . . . . . 
```

```
The next value is (value < minimum), place it below the descending column and update the minimum.
               |. . . 2 7 6 3 8
. . . . . . . 1 4 5 . . . . . . 
```

```
The next value is (minimum <= value < maximum), place new values ​​(maximum and minimum) in ascending order column,
and let the value group arranged so far be Part.(Part: 145 completed)
               |. . . . 7 6 3 8
. . . . . . .|1 4 5|2 . . . . . 
```

```
The next value is (maximum <= value), place it above the ascending order column and update the maximum.
               |. . . . . 6 3 8
. . . . . . .|1 4 5|2 7 . . . . 
```

```
The next value is (minimum <= value < maximum), place new values ​​(maximum and minimum) in ascending order column,
and let the value group arranged so far be Part.(Part: 27 completed)
               |. . . . . . 3 8
. . . . . . .|1 4 5|2 7|6 . . . 
```

```
The next value is (value < minimum), place it below the descending column and update the minimum.
               |. . . . . . . 8
. . . . . . 3|1 4 5|2 7|6 . . . 
```

```
The next value is (maximum <= value), place it above the ascending order column and update the maximum.(Part: 368 completed)
               |. . . . . . . .
. . . . . .|3|1 4 5|2 7|6 8|. . 
```

```
External area result.
4 5|2 7|6 8|. .  Ascending column arrangement
. . . . . .|3|1  Descending column arrangement
4 5|2 7|6 8|3|1  Actual arrangement 
```

```
Merge generated Parts.
145  27  368
12457  368
12345678
Sort complete. 
```

。

# 改进

我们将对基本算法进行额外的改进。

*   执行插入排序以确保零件的长度。
*   顺序合并以避免递归。

# 我们验证过的环境

*   Windows 10 专业版 64 位
*   酷睿 i7-8700 3.20 GHz
*   用于 x64 的 Microsoft(R) C/C++优化编译器版本 19.16.27030.1
*   clang 版本 8.0.0(标签/发行版 _ 800/最终版)
*   gcc 版本 8.3.0 (Rev2，由 MSYS2 项目构建)

# 随机数基准

对从同一种子生成的浮点值进行排序。
单位是秒，数字越小越快。
[![Random_10000.png](img/44d0b7e269f38e9e035b56578d0b1611.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aejuueRO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/379361/4e82765c-2b15-9334-063c-6ca2e0894472.png)
[![Random_1000000.png](img/e5481987337811f52014b1ed747cde94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c2tA94cl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/379361/530577cd-a21e-9d32-d801-3ca85a675cc5.png)
[![Random_100000000.png](img/e1c41028a6a9277fbd95cd2ed42ff800.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K-0leKyE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/379361/caed6f1f-1ff4-a798-66fc-187949451596.png)

# 条件基准

以下是对浮点值[100，000，000]进行排序的数组。
单位是秒，数字越小越快。
[![Msvc.png](img/450f6ba8749e4d8060b4d7b4499725b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tqiI8gdg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/379361/8af35731-e0e7-f346-792c-f3944345c82f.png)
[![clang++.png](img/eb35326d4245d7df9040ea26caef30d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o1tw0n3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/379361/16872a88-51ec-00d0-31eb-44f0994cd23c.png)
[![g++.png](img/01aa2e530ee799514b27cdcfc5a1dda0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUlWRRxP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/379361/bbca437a-7977-e03f-159a-db5977506f79.png)

# 终于

怎么样？

Hayate-Shiki 是一种稳定的排序，但对随机数有很强的特征。
在条件基准测试中，发现编译器的优化特性而不是算法特性的影响变得很强，以至于几乎不能作为判断材料。

合并排序赢得快速排序的那一天会到来吗？

排序算法还是很浪漫的。

感谢观看！