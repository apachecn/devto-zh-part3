# 对 JavaScript 越来越着迷

> 原文：<https://dev.to/dallas/getting-loopy-with-javascript-4j4p>

这里有五种方法可以让你在 JavaScript 中循环 5 次，每次都用不同的值来做一些事情:

```
/* totally manual with a while loop */
let i = 0;
while (i < 5) {
  console.log(`Hello, ${i}!`);
  i++;
};

/* classic for loop */
for (let j = 0; j < 5; j++) {
  console.log(`Hello there, ${j}!`);
}

/* new-age for...of loop */
for (let k of [0, 1, 2, 3, 4]) {
  console.log(`Hello once more, ${k}!`);
}

/* using forEach with manually filled array */
[0, 1, 2, 3, 4].forEach(
  l => console.log(`Hello again, ${l}!`)
);

/* auto-fill an array of length 5 and use indices */
Array(5).fill().forEach(
  (_,m) => console.log(`Hello one last time, ${m}!`)
); 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中还有哪些方法可以循环特定的次数？

温暖的问候，
达拉斯