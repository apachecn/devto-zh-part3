# javascript 中的排序算法

> 原文：<https://dev.to/delph/sorting-algorithms-in-javascript-1797>

## 冒泡排序

<figure>

[![alt text](img/829853f5179eb21c661d13c48e49cc96.png "bulba-sort")](https://res.cloudinary.com/practicaldev/image/fetch/s--YGFbR3MD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.pokemonpets.cimg/monsters-images-300-300/1-Bulbasaur.png)

<figcaption>Fun fact: if you say bubble sort really fast, it sounds like bulbasaur :O</figcaption>

</figure>

冒泡排序是一种简单的排序算法，它反复遍历要排序的列表，比较每对相邻的项目，如果它们的顺序不对，就交换它们。

重复遍历列表，直到不需要交换，这表示列表已排序。

该算法不适合大数据集，因为其平均和最坏情况下的时间复杂度为 O(n*2)。

<figure>[https://www.youtube.com/embed/koMpGeZpu4Q](https://www.youtube.com/embed/koMpGeZpu4Q)

<figcaption>连奥巴马都不以为然</figcaption>

</figure>

最好的时间复杂度是ω(n ),这发生在数组已经排序的时候。

```
function bubbleSort(arr) {
    let swapped, tmp;
    do {
        swapped = false;
        for (let i=0; i< arr.length -1 ; i++) {
            if(arr[i] > arr[i+1]) {
                tmp = arr[i];
                arr[i] = arr[i+1];
                arr[i+1] = tmp;
                swapped = true;
            }
        }
    } while (swapped)
    return arr
} 
```

## 选择排序

选择排序是一种简单的排序算法，它遍历数组并保存绝对最小值。然后将最小值与未排序数组中的第一项交换。

最好和最坏情况的时间复杂度是 O(n*2)。

```
function selectionSort(arr) {
    for (let i =0; i < arr.length; i++) {
        let min = i
        for (let j=i+1; j<arr.length; j++) {
            if (arr[j] < arr[min]) {
                min = j
            }
        }
        if (i !== min) [arr[i], arr[min]] = [arr[min], arr[i]]
    }
    return arr
} 
```

## 插入排序

使用插入排序，您可以将不在正确位置的元素一直移动到它应该在的位置。

该算法不适合大数据集，因为其平均和最坏情况下的时间复杂度为 O(n*2)。最好的时间复杂度是ω(n ),这发生在数组已经排序的时候。

```
function insertionSort(arr) {
    for (let i = 1; i < arr.length; i ++) {
        for (let j =0; j < i; j++) {
            if (arr[i] < arr[j]) {
                let tmp = arr.splice(i, 1)
                arr.splice(j,0,tmp[0])
            }
        }
    }
    return arr
} 
```

## 合并排序

Merge sort 将数组分成两半，为这两半调用自身，然后合并这两半。

merge 算法由两个函数组成:mergeSort 函数，负责对数组进行分区；merge 函数，合并分离的数组。

<figure>

[![alt text](img/922a5d1b8feae4b4b317457b1c35bad5.png "merge sort")](https://res.cloudinary.com/practicaldev/image/fetch/s--0BjAq9j_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ds055uzetaobb.cloudfront.net/image_optimizer/8c074d46d4c96077d11f9e8cab9ff5d95bdc3da0.gif)

<figcaption>here is a visual representation</figcaption>

</figure>

合并排序利用了递归，是最受尊敬的搜索算法之一。其最坏的时间复杂度为 O(n*logn(n))。然而，最好的情况时间复杂度也是ω(n * logn(n))

```
function mergeSort(arr) {
  if (arr.length === 1) return arr;
  const mid = Math.floor(arr.length / 2);
  const left = arr.slice(0, mid);
  const right = arr.slice(mid);
  return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right) {
  let result = [];
  let li = 0; //left index
  let ri = 0; //right index
  while (li < left.length && ri < right.length) {
    if (left[li] < right[ri]) {
      result.push(left[li]);
      li++;
    } else {
      result.push(right[ri]);
      ri++;
    }
  }
  return result.concat(left.slice(li)).concat(right.slice(ri));
} 
```