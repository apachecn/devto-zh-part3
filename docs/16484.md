# JS 的花式函数参数

> 原文：<https://dev.to/chenge/fancy-function-parameters-of-js-52b6>

代码来自[这个帖子](https://www.javascriptjanuary.com/blog/fancy-function-parameters)。这使得参数更加清晰。

```
function renderList(list, { 
    ordered = true,
    color = '#1e2a2d',
    bgColor = 'transparent'
} = {}) {
    /* ... */
}

// simple use
renderList(['love', 'patience', 'pain'])

// with all arguments
renderList(['one', 'two'], { ordered: true, color: '#c0ffee', bgColor: '#5ad' })

// with only one optional argument (bgColor)
renderList(['one', 'two'], { bgColor: '#5ad' }) 
```