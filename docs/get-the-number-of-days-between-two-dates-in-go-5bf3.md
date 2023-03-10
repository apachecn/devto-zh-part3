# 获取 Go 中两个日期之间的天数

> 原文：<https://dev.to/samwho/get-the-number-of-days-between-two-dates-in-go-5bf3>

最近，我需要找出 Go 中两个日期之间的天数，并且不断遇到不太符合我需要的实现。

所以我卷了我自己的，我很自豪它是如此的结实！:)

```
package main

import (
    "fmt"
    "time"
)

func main() {
    fmt.Println(daysBetween(date("2012-01-01"), date("2012-01-07"))) // 6
    fmt.Println(daysBetween(date("2016-01-01"), date("2017-01-01"))) // 366 because leap year
    fmt.Println(daysBetween(date("2017-01-01"), date("2018-01-01"))) // 365
    fmt.Println(daysBetween(date("2016-01-01"), date("2016-01-01"))) // 0
}

func daysBetween(a, b time.Time) int {
    if a.After(b) {
        a, b = b, a
    }

    days := -a.YearDay()
    for year := a.Year(); year < b.Year(); year++ {
        days += time.Date(year, time.December, 31, 0, 0, 0, 0, time.UTC).YearDay()
    }
    days += b.YearDay()

    return days
}

func date(s string) time.Time {
    d, _ := time.Parse("2006-01-02", s)
    return d
} 
```