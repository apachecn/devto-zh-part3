# 短视频:数组迭代

> 原文：<https://dev.to/chenge/short-video-array-iteration-c00>

JS 掘金频道对 JS 的短视频很好。

[https://www.youtube.com/embed/RD69hYGY-4U](https://www.youtube.com/embed/RD69hYGY-4U)

以下代码与你分享。

```
//forEach
[1,2,3].forEach(function(item, index){
  console.log(item, index)
})

//map
const three = [1,2,3]
const doubled = three.map(function(item){
  return item * 2
})
console.log(doubled)

//filter
const ints = [1,2,3]
const evens = ints.filter(function(item){
  return item % 2 === 0
})
console.log(evens)

//reduce
const sum = [1,2,3].reduce(function(result, item){
  return result + item
})
console.log(sum)

//some
const hasNegativeNumbers = [1,2,3,-1,4].some(function(item){
  return item < 0
})
console.log(hasNegativeNumbers)

//every
const allPositiveNumbers = [1,2,3].every(function(item){
  return item > 0
})
console.log(allPositiveNumbers)

//find
const objects = [{id: 'a'}, {id: 'b'}, {id: 'c'}]
const found = objects.find(function(item){
  return item.id === 'b'
})
console.log(found)

//find index
const objects2 = [{id: 'a'}, {id: 'b'}, {id: 'c'}]
const foundIndex = objects2.findIndex(function(item){
  return item.id === 'b'
})
console.log(foundIndex) 
```