# JavaScript 中急需的 filterMap

> 原文：<https://dev.to/akashkava/much-needed-filtermap-in-javascript-12ma>

最常见的情况是，我们先执行`filter`，然后执行`map`，事实证明，将两者结合在一个函数中肯定会避免多次迭代。

```
Array.prototype.filterMap = function(filter) {
   const r = [];
   for(let i=0; i<this.length; i++) {
      const item = filter(this[i], i, this);
      if (item !== undefined) {
         r.push(item);
      }
   }
   return r;
};

// usage..

emails = customers.filterMap(
   (c) => c.active ?
      { name: c.name, email: c.email } :
      undefined);

// as opposed to

emails = customers.filter( (c) => c.active)
            .map( (c) => ({ name: c.name, email: c.email })); 
```

Enter fullscreen mode Exit fullscreen mode

理想情况下，我们可以改变`Array.map`的行为来适应`Array.filterMap`，但是我们不知道这会对众多的库产生什么影响。

性能:[https://jsperf.com/array-filtermap/1](https://jsperf.com/array-filtermap/1)