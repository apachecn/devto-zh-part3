# 可怕的如何得到中间的首字母

> 原文：<https://dev.to/samrocksc/horrible-howto-get-that-middle-initial-5e5j>

我真的只是想发布我的第一个帖子，我刚刚做了这个片段，所以我决定在这里发布！什么干草！

```
const formatMiddleInitial = middleName => (middleName ? middleName.charAt(0).toUpperCase() : null);
console.log('stuff', formatMiddleInitial('clark hark')); 
```