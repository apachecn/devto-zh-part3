# 对象初始化相同的关键字和值

> 原文：<https://dev.to/yashwanth2804/obj-initilization-same-keyandvalue-5cn2>

```
var k_as_v = "k_as_v";
obj = {

    [k_as_v]:k_as_v,

}

console.log(obj); 
```

为什么我不能保持 k_as_v

```
var k_as_v ="k_as_v";

obj = {

   [k_as_v],

}

console.log(obj); 
```

如果不能，我怎么能做到这一点没有重新声明，任何黑客！