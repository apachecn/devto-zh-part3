# Bootstrap 不使用完整的 jQuery

> 原文：<https://dev.to/wincentbalin/bootstrap-does-not-use-complete-jquery-504e>

如果你想使用 [Bootstrap 框架](https://getbootstrap.com/)，你还将包括 [jQuery 库](https://jquery.com/)，例如:

```
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script> 
```

Enter fullscreen mode Exit fullscreen mode

如果你注意 jQuery 收录的文件名，你会看到这是 [*瘦身*](https://stackoverflow.com/questions/35424053/what-are-the-differences-between-normal-and-slim-package-of-jquery) 版本的库。比如它没有`$.get()`功能。

如果您想执行 AJAX，或者使用精简版本中不包含的其他函数，请包含完整的 jQuery，在上面的例子中:

```
<script src="https://code.jquery.com/jquery-3.3.1.min.js" integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT" crossorigin="anonymous"></script> 
```

Enter fullscreen mode Exit fullscreen mode