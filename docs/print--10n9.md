# 将*打印为方框

> 原文：<https://dev.to/mu/print--10n9>

```
maxRows = 10
maxCols = 100
for i in range(maxRows):
    star = "*"
    for j in range(maxCols-2):
        if(i==0 or i==maxRows-1):
            star = star + "*"
        else:
            star = star + " "
    print(star+"*") 
```

Enter fullscreen mode Exit fullscreen mode

[![printStar](img/fb5985fbbb9ac438c946cfc18502e464.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BYkq2oVY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0qlba6ydvih51lo4lfxw.png)