# 在代理后设置当前日期

> 原文：<https://dev.to/adzubla/set-current-date-behind-a-proxy-4hk>

如果你的系统在代理之后，你不能使用 NTP，你可以试试这个小技巧:

```
sudo date -s "$(curl -s http://www.timeapi.org/utc/now)" 
```

请注意，一段时间后，您的计算机时钟可能会偏离标准时钟。所以这只是权宜之计。