# 按类别对 WordPress 文章进行分组

> 原文：<https://dev.to/cjwd/grouping-wordpress-posts-by-category-3kc7>

假设我们希望我们的帖子显示如下。

**类别 1**

*   职位 1
*   职位 2
*   职位 3

**类别 2**

*   职位 1
*   职位 2
*   职位 3

你会很快意识到使用标准的 WordPress 循环你不会得到你想要的。

## 问题

使用传统的循环，您将只能访问那篇文章的类别(术语),结果如下

**类别 1**
岗位 1
**类别 1**
岗位 2
**类别 1**
岗位 3

## 解

解决方案其实很简单。首先获取术语列表，循环遍历每个术语，在这个循环中获取带有该术语标签的帖子。我将使用 [get_terms](https://developer.wordpress.org/reference/functions/get_terms/) ，它是一个助手，最终调用 [WP_Term_Query](https://developer.wordpress.org/reference/classes/wp_term_query/__construct/) 首先获得术语列表。

```
<?php
$terms = get_terms( array(
  "taxonomy" => "category",
) ); 
```

上面的代码返回了一个 term 对象的数组，带有诸如 term_id 这样的属性。现在我们有了这些术语，我们将遍历它们，将 term_id 传递给 [get_posts()](https://codex.wordpress.org/Template_Tags/get_posts) 函数，以获得带有该术语标签的文章列表。get_posts()最终调用 [WP_Query](https://codex.wordpress.org/Class_Reference/WP_Query) ，如果您的情况需要，您可以使用该类。在我的例子中，我只想要文章的标题。
我们的代码现在变成如下:

```
<?php
$terms = get_terms( array(
  "taxonomy" => "category",
) );

foreach ($terms as $term) : 
  $posts = get_posts(["post_type" => "store", "cat" => $term->term_id]);
?>
  <h4><?= $term->name; ?></h4>
  <ul>
  <?php foreach($posts as $post) : ?>
    <li><?= $post->post_title; ?></li>
  <?php endforeach; ?>
  </ul>
<?php endforeach; ?> 
```