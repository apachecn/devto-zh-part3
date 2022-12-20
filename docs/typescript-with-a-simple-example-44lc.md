# 带有简单示例的类型脚本

> 原文：<https://dev.to/azwift/typescript-with-a-simple-example-44lc>

Typescript 是一种编译成 Javascript 的开源编程语言。Typescript 是由 Microsoft 开发和支持的。

## 为什么要打字稿

Typescript 提供了许多特性，这些特性使它成为比 Javascript 更好的选择。举几个例子，

*   它引入了类型安全
*   支持命名空间、模块、接口和声明文件
*   可以使用 private 和 protected 关键字声明类方法和属性
*   提供代码完成和智能感知
*   与流行的 [Javascript 库](https://www.typescriptlang.org/samples/index.html) (React、Angular、Vue、Express 等)集成。)

## 举例

我们要看的例子是一个[堆排序](https://en.wikipedia.org/wiki/Heapsort)的实现。要求如下:

*   我们希望能够对任何类型的数组进行排序
*   我们希望能够指定排序的顺序(升序或降序)
*   我们希望能够提供一个我们自己的比较函数作为回调

为了给我们的需求打下基础，我们首先定义一个代表排序选项的接口。

我们希望我们的比较函数接受两个输入参数。此外，如果两个值相等，我们希望它返回 0，如果第一个值大于第二个值，返回 1，否则返回-1。

```
interface SortOptions{

  order: "asc" | "desc";
  compare: ICompareFunction;

}

// describes the input and output expected from a compare function
interface ICompareFunction {

    (a: any, b: any): number;

} 
```

这些是将用于实现我们的堆排序的主要函数。

```
function heapsort(data: any[], options?: SortOptions): void{}
function heapify(data: any[], length: number, options: SortOptions): void{}
function bubbleDown(data: any[], curr: number, length: number, options: SortOptions): void{} 
```

我们用一个*？*符号表示可选输入。

## 实现

### 堆排序

函数“heapsort”使用 Heapsort 算法对数组进行排序。

如果我们按升序排序，我们希望建立一个最大堆，否则我们建立一个最小堆。我们通过调用“heapify”函数来实现这一点。

然后，我们将 max 或 min 与堆的最后一个元素交换，并在堆的根(数组中的第一个元素)上调用我们的“bubbleDown”函数。我们对阵列的长度重复这个步骤。

如果没有提供“options”参数，我们将手动将选项设置为默认值。

```
 function sortHeap(data: any[], options?: SortOptions): void{

    //set default options if not provided
    if(!options){
      options =  {
        compare : defaultCompare,
        order: 'asc'
      };
    }   
    if(!options.order)
      options.order = 'asc';

    if(!options.compare)
      options.compare = defaultCompare;    

    //turn array into a heap
    heapify(data, data.length, options);

    swap(data, data.length - 1 , 0);

    for(let i = data.length - 2; i >= 0; i--){
      bubbleDown(data, 0, i + 1, options);

      //swap max or min with the last element in the heap
      swap(data, i, 0);
    }
  }

 //helper functions
 function swap(data: any[], i:number, j:number){
    let temp = data[i];
    data[i] = data[j];
    data[j] = temp;
 }

 //default compare
 function defaultCompare (a: number, b: number): number{
    if(a == b)
        return 0;
    return (a < b) ? -1 : 1;
 } 
```

### 充饥

“heapify”函数将一个数组重新排列到一个最小或最大堆中。我们实现了这一点，但是向下冒泡堆的所有非叶节点，就像在数组前半部分的所有节点中一样。

```
function heapify(data: any[], length: number, options: SortOptions): void{

   for(let i = length/2; i >= 0; i--)
      this.bubbleDown(data, Math.floor(i), length, options);
} 
```

### 气泡下降

函数“bubbleDown”接受一个堆，并递归地向下冒泡索引“curr”处的元素，直到索引“length”。我们还提供了上述排序选项。

```
 private bubbleDown(data: any[], curr: number, length: number, options: SortOptions): void{

    const asc: boolean = (options && options.order == "desc") ? false : true;

    //base case
    if(!data || curr >= length)
        return;

    //indexes for left and right node 
    const l: number = (2 * curr + 1 >= length) ? null : 2 * curr + 1;
    const r: number = (2 * curr + 2 >= length) ? null : 2 * curr + 2;

    //find max or min element
    let maxmin: any;
    if(!data[l] && !data[r])
        return ;
    else if(!data[l])
      maxmin = data[r];
    else if(!data[r])
      maxmin = data[l];
    else{
      const result: number = options.compare(data[l], data[r]);
      maxmin = ((result < 0 && asc) || (result > 0 && !asc)) ? data[r] : data[l];
    }

    //compare max/min of child nodes with current element
    const result: number = options.compare(data[curr], maxmin);
    if((asc &&  result < 0) ||  (!asc && result > 0 )) {

      //swap with largest or smallest element (depending on asc) and swap index
      let temp = data[curr];
      data[curr] = maxmin ;

      //swap r or l with curr
      curr = options.compare(maxmin, data[l]) === 0 ? l : r;
      data[curr] = temp;
      return this.bubbleDown(data, curr, length, options);
    }

  } 
```

希望本文证明了 Typescript 提供了使用 Javascript 开发大规模应用程序时通常缺乏的健全性和结构。

上面的代码以及其他一些排序算法可以在 [Github](https://github.com/azwift/sort-collection-ts) 上找到。

如果你对 Typescript 感兴趣，这是一个好的开始。

*打字——安全！*