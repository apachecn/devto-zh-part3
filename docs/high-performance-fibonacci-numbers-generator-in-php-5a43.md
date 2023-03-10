# PHP 中的高性能斐波那契数生成器

> 原文：<https://dev.to/rafaelbernard/high-performance-fibonacci-numbers-generator-in-php-5a43>

基于文章[Go 中的高性能斐波那契数生成器](https://blog.abelotech.com/posts/fibonacci-numbers-golang/)我用 PHP 编写了我的版本。尽管 PHP 和 Go 架构之间的差异反映在响应时间上，但当使用优化的函数时，我们可能会面临巨大的性能差异。我们可能会注意到，我们可以得到相同的结果，但是编写代码的质量可以改变很多事情。

**递归方法**

```
function fibonacci(int $n):int { 
    if ($n <= 1) { 
        return $n; 
    } 

    return fibonacci($n-1) + fibonacci($n-2);
} 
```

Enter fullscreen mode Exit fullscreen mode

基准和测试

```
function test_fibonacci() {
  $data = [
    [0,0], [1,1], [2,1], [3,2], [4,3], [5,5], [6,8], [10,55], [42,267914296]
  ];

  foreach($data as $test) {
    $result = fibonacci($test[0]);
    if ($result !== $test[1]) {
      throw new \UnexpectedValueException("Error Processing Request. N: {$test[0]}, got: {$result}, expected: {$test[1]}", 1);
    }
  }

  echo "Tests - Success.".PHP_EOL;
}

/**
  * From https://gist.github.com/blongden/2352583
  */
function benchmark($x)
{
    $start = $t = microtime(true);
    $total = $c = $loop = 0;
    while (true) {
        $x();
        $c++;
        $now = microtime(true);
        if ($now - $t > 1) {
            $loop++;
            $total += $c;
            list($t, $c) = array(microtime(true), 0);
        }
        if ($now - $start > 2) {
            return round($total / $loop);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
Benchmark 10 run: 163,754/sec or 0.0061067210571955ms/op
Benchmark 20 run: 1,351/sec or 0.74019245003701ms/op 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，计算 20 个斐波那契数比计算 10 个斐波那契数要多花 123 倍的时间。一点都没演好！在链接的文章中可以找到解释。

**循序渐进**

```
function fibonacci_tuned(int $n):float {
  if ($n <= 1) {
    return $n;
  }

  $n2 = 0;
  $n1 = 1;

  for ($i = 2; $i < $n; $i++) {
    $n2_ = $n2;
    $n2 = $n1;
    $n1 = ($n1 + $n2_);
  }

  return $n2 + $n1;
}

function test_fibonacci_tuned() {
  $data = [
    [0,0], [1,1], [2,1], [3,2], [4,3], [5,5], [6,8], [10,55], [42,267914296]
  ];

  foreach($data as $test) {
    $result = fibonacci_tuned($test[0]);
    $float_test_value = (float) $test[1];
    if ($result !== $float_test_value) {
      throw new \UnexpectedValueException("Error Processing Request. N: {$test[0]}, got: {$result}, expected: {$float_test_value}", 1);
    }
  }

  echo "Tests - Success.".PHP_EOL;
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
Benchmark 10 tuned run: 3,345,999/sec or 0.00029886440492062ms/op
Benchmark 20 tuned run: 2,069,100/sec or 0.00048330191870862ms/op 
```

Enter fullscreen mode Exit fullscreen mode

作为一个更好的场景，计算 20 个数字比计算 10 个数字几乎要多花两倍的时间。有道理。而且表现不错！

考虑到这两种方法，递归方法运行 10 次斐波纳契数运算的时间是顺序方法的 20 倍，对于 20 次斐波纳契数运算，时间是顺序方法的 1，824 倍。

PHP 中的斐波那契实现可以在[https://github.com/rafaelbernard/php-fibonacci](https://github.com/rafaelbernard/php-fibonacci)找到。