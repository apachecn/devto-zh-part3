# 如何在 Javascript 中移除数组中的重复对象

> 原文：<https://dev.to/skptricks/how-to-remove-duplicate-objects-from-an-array-in-javascript-553b>

帖子链接:[如何在 Javascript 中移除数组中的重复对象](https://www.skptricks.com/2018/11/how-to-remove-duplicate-objects-from-an-array-in-javascript.html)

本教程解释了如何在 javascript 中删除数组中的重复对象。让我们看下面的例子来建立更多的理解，并希望可以解决你未来的编码问题。我们使用 ES6 映射和过滤方法从阵列中删除重复的对象。

如何在 javascript 中移除数组中的重复对象

从数组中删除重复的对象:
让我们考虑下面这个 id 为、名称为的对象数组。

const arr = [
{ id: 2，name: "sumit" }，
{ id: 1，name: "amit" }，
{ id: 3，name: "rahul" }，
{ id: 4，name: "jay" }，
{ id: 2，name: "ra one" }，
{ id: 3，name: "alex" }，
{ id: 1，name: "devid" }，
{ id: 7，name: "sam" }，
。

在 javascript 中，我们使用下面的链式函数来过滤出唯一的数组值并从数组中删除重复的对象，

函数 getUnique(arr，index) {

const unique = arr
map(e = > e[index])

```
 // store the keys of the unique objects
   .map((e, i, final) => final.indexOf(e) === i && i)

   // eliminate the dead keys & store unique objects
  .filter(e => arr[e]).map(e => arr[e]); 
```

返回唯一；
}

console.log(getUnique(arr，" id ")

实施后，我们将获得以下输出:

> 数组
> [Object { id: 2，name: "sumit" }，
> Object { id: 1，name: "amit" }，
> Object { id: 3，name: "rahul" }，
> Object { id: 4，name: "jay" }，
> Object { id: 7，name: "sam" }]

[![](img/6dbc5c2a56d418afaeb38cd1fe71b7dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zqn-yxI0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-cZTXKmDZyow/W_-MVXbTyvI/AAAAAAAACLs/U9Mohvy1dPEZw-DUu4Ayu2IuTP9nslZTQCLcBGAs/s400/dup.png)