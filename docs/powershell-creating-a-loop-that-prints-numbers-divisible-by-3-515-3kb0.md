# powershell 创建一个循环来打印可被 3、5、15 整除的数字？

> 原文：<https://dev.to/dj_fuego24/powershell-creating-a-loop-that-prints-numbers-divisible-by-3-515-3kb0>

函数打印-数字-怪异([int]$count) {

```
for($i=0; $i -le $count; $i = $i + 1) {

    $isdivisibleby3 = $i % 3 -eq 0
    $isdivisibleby5 = $i % 5 -eq 0
    $isdivisibleby15 = $i % 15 -eq 0

   if($isdivisible) {
       Write-host $i "- Even"
    }
    else {
       Write-host $i "- Odd"
    }

    if($i -eq 3) 
    {
        Write-host "divisible by 3"
    }
    elseif($i -eq 5) 
    {
        Write-host "divisible by 5"
    }
    else {
        Write-host "divisible by 15"
} 
```

}