# lodash 按对象属性查找

> 原文：<https://dev.to/vicoerv/lodash-find-by-object-property-57ec>

你有没有想过如何用一行代码从对象的属性中获取数组？然后尝试使用**lodash/find**T2】

```
npm install lodash/find # or yarn add lodash/find 
```

```
import find from 'lodash/find'

const obj = [
  { name: 'vico', level: 1 },
  { name: 'ben', level: 99 }
]

const vico = find(obj, ['name', 'vico'])

console.log(vico)
// output: { name: 'vico', level: 1 } 
```