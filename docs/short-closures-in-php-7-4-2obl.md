# PHP 7.4 中的短闭包

> 原文：<https://dev.to/brendt/short-closures-in-php-7-4-2obl>

短闭包是在 PHP 中编写更简洁的短函数的一种方式:

```
// A collection of Post objects
$posts = [/* … */];

$ids = array_map(fn($post) => $post->id, $posts); 
```

你可以在这里读到关于他们的一切:[https://stitcher.io/blog/short-closures-in-php](https://stitcher.io/blog/short-closures-in-php)