# 你最喜欢的算法是什么？

> 原文：<https://dev.to/adtm/whats-your-favorite-algorithm-4bo8>

这是我最喜欢问技术招聘人员的问题——每个人都有一个独特的故事，你可以更好地了解这家公司和这个人，当然，随后很容易进行对话。我听说了各种洗牌算法，布鲁姆过滤器，超对数等等。

我的更多的是一种技术，但我真的很喜欢它，因为它很简单。
包括下面的一个小解释*(出于好奇)*。

你最喜欢的算法是什么？:)

```
/* 
 * Question:
 * Given an array of integers from 1 <= A[i] <= n, 
 * some elements repeat twice and others only once.
 *
 * Your task is to find the elements which repeat twice.
 * Without using any extra space and in O(n) time.
 */

/*
 * The key here is not to use a hashmap, O(N) space, 
 * but to flip the numbers from positive to negative.
 *
 * When you traverse the array you can map the number 5 to index 4 
 * (we will check by sign and not value for duplicates) and if it is not negative 
 * - meaning it has not repeated - negate it. If it is negative, you know it has been 
 * already encountered. 
 * 
*/

const A = [5,2,1,4,2,1,7];

function findDuplicates(A) {

 const oldLength = A.length;
 for (let i = 0; i < oldLength; ++i) {
   const value = Math.abs(A[i]) - 1; // ignoring the sign and get the value

   if (A[value] > 0) A[value] *= -1;
   else A.push(value + 1);
 }

 return A.slice(oldLength);
} 
```

Enter fullscreen mode Exit fullscreen mode