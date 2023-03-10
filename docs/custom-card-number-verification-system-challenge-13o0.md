# 自定义卡号验证系统挑战

> 原文：<https://dev.to/kevinmungai/custom-card-number-verification-system-challenge-13o0>

# 告诫

该编程挑战位于

> [http://neo.cellulant.com:3000/sw_internship.html](http://neo.cellulant.com:3000/sw_internship.html)

或者

> [谷歌网络缓存](http://webcache.googleusercontent.com/search?q=cache:http://neo.cellulant.com:3000/sw_internship.html)

并且将**按原样呈现**:

# 挑战

> [Cellulant](https://www.cellulant.com/) 实施了定制的卡号验证系统，以确保检测和阻止假卡。刮刮卡由 **4** 组 **5** 位(共 20 位)组成，如 **10006-12342-00081-99993** ，用空格或破折号隔开打印。
> 
> 每组 5 位数字由两部分组成，前 4 位数字(数字)和第 5 位数字(校验和)。该公式规定每组数字必须按如下方式进行验证:前 4 位数字(例如 1234)作为十进制数被转换为八进制数(基数为 8 ),即 23228。然后处理这个八进制数以产生校验和，如下所示:
> 
> 1.  将所有数字相加得到一个新的数字**“X”**
> 2.  如果**【X】**大于 1 位数，重复 **(1)** 步骤，直到得到一位数。**【Y】**
> 3.  将**“Y”**追加到原始十进制数的末尾，例如 1234 **2**
> 
> #### 例如:
> 
> **给定设置“10006”**
> 
> 把 1000 转换成八进制= > 1750 >
> 1+7+5+0 = 15
> 1+5 = 68
> 有效数是 10006 所以；
> 返回**(真)**
> 
> **给定设置“99998”**
> 
> 将 9999 转换为八进制= > 23417
> 2+3+4+1+7 = 21
> 2+1 = 3
> 有效数为 99993 so
> 返回**(假)**
> 
> 使用**函数**创建一个 **Java** 、 **Java-Script** 或 **PHP** 脚本，该脚本将接收一个**字符串**值**(暂存卡号)**，并根据卡号是否有效返回布尔值 TRUE 或 FALSE，如下所示
> 
> 你应该在你的代码中使用许多你认为必要的其他函数。

# 真齐解

该解决方案由[菲利普·拉马尔霍](https://dev.to/filiperamalho)提供

1.  我们得到前四个和 7 的模运算
2.  然后我们从校验和中减去结果
3.  接下来，我们用 7 对上面的结果进行模运算
4.  然后我们检查它是否等于 0

菲利普·拉马尔霍对此有更好的解释:

> 对于 9999|8
> 
> 1.  9999% 7 = 3//首先你模到 7
> 2.  8-3 = 5//然后从校验和中减去结果。
> 3.  5% 7 = 5//然后你对 7 取模。
> 
> 最终结果不是 0，所以数字无效

```
(defn  is-valid?  [number]  (let  [first-four  (quot  number  10)  check-sum  (rem  number  10)]  (->  first-four  (mod  7)  (-  check-sum)  (mod  7)  (=  0))))  user>  (is-valid-modified?  10006)  true  user>  (is-valid-modified?  99993)  true  user>  (is-valid-modified?  99998)  false  user>  (is-valid-modified?  12342)  true  user>  (is-valid-modified?  11697)  true 
```

Enter fullscreen mode Exit fullscreen mode

```
(defn  validate-scratch-card  [card-number]  (let  [f  (comp  is-valid?  #(Integer/parseInt  %))]  (->>  (string/split  card-number  #"(-|\s)")  (map  f)  (every?  true?))))  user>  (validate-scratch-card  "10006 12342 00081 99998")  false  user>  (validate-scratch-card  "10006 12342 00081 99993")  true 
```

Enter fullscreen mode Exit fullscreen mode

这是一个优雅的解决方案。

# 旧解

我将使用 **Clojure** ，而不是使用上面提出的任何语言。

`validate-scratch-card`是获取**字符串**的函数，可能类似于:

1.  "`10006-12342-00081-99993`"
2.  "`10006 12342 00081 99993`"

根据空格或连字符的存在来分割参数会很好，这样我就可以处理每个子串:

```
;; has hyphen  user>  (clojure.string/split  "10006-12342-00081-99993"  #("-|\s)")  ;; ["10006" "12342" "00081" "99993"]  ;; has spaces  user>  (clojure.string/split  "10006 12342 00081 99993"  #"(-|\s)")  ;; ["10006" "12342" "00081" "99993"] 
```

Enter fullscreen mode Exit fullscreen mode

议程上的下一项是通过 Java 方法`Integer/parseInt`将每个单独的子字符串解析为`Integer`。

```
user>  (Integer/parseInt  "10006")  ;; 10006 
```

Enter fullscreen mode Exit fullscreen mode

`map`可以用来解析每个单独的子串

```
user>  (map  #(Integer/parseInt  %)  "10006 12342 00081 99993")  ;; 10006 
```

Enter fullscreen mode Exit fullscreen mode

可以根据上面的规则对每个整数的有效性进行检查。
每个整数有 2 个部分:

1.  前四位数字(数字)
2.  第五位数字(校验和)

```
user>  (quot  10006  10)  ;; 1000   first four digits (the number)  user>  (rem  10006  10)  ;; 6   fifth digit (checksum) 
```

Enter fullscreen mode Exit fullscreen mode

接下来是将前四位数转换成八进制。

这将通过创建基本转换器
来实现

```
(defn-  to-digits  [number  base]  (loop  [n  number  digits  '()]  (if  (pos?  n)  (recur  (quot  n  base)  (conj  digits  (mod  n  base)))  digits)))  (defn  to-octal  [number]  "returns a list of octal numbers"  (to-digits  number  8)) 
```

Enter fullscreen mode Exit fullscreen mode

```
user>  (to-octal  1000)  ;; (1 7 5 0) 
```

Enter fullscreen mode Exit fullscreen mode

接下来的步骤是对每个生成的数字求和。

但在此之前，需要一个取两个八进制数并求和的函数。

```
(defn  add-two-octal  [x  y]  "add two octal numbers"  (let  [a  (Integer/parseInt  (str  x)  8)  b  (Integer/parseInt  (str  y)  8)]  (->  (+  a  b)  Integer/toOctalString  Integer/parseInt))) 
```

Enter fullscreen mode Exit fullscreen mode

```
user>  (add-two-octal  3  6)  ;; 11 
```

Enter fullscreen mode Exit fullscreen mode

这个`add-two-octal`函数可以用来将一系列八进制数加在一起，如下所示:

```
user>  (reduce  #(add-two-octal  %1  %2)  (to-octal  1000))  ;; 15  user>  (reduce  #(add-two-octal  %1  %2)  '(1  7  5  0))  ;; 15 
```

Enter fullscreen mode Exit fullscreen mode

虽然加法工作得很好，但它不满足总和的结果应该是个位数的要求。如果求和的结果是一个位数，则应返回该位数，否则应再次求和。
如果新的总和恰好是两位数，还需要一个函数来分隔它。

```
(defn  separate  [number]  "a utility function to separate digits"  (loop  [n  number  digits  '()]  (if  (zero?  n)  digits  (recur  (quot  n  10)  (conj  digits  (rem  n  10))))))  (defn  sum-octal  [number]  (let  [sum  (reduce  #(add-two-octal  %1  %2)  (to-octal  number))]  (if  (zero?  (quot  sum  10))  sum  (apply  add-two-octal  (separate  sum))))) 
```

Enter fullscreen mode Exit fullscreen mode

```
user>  (sum-octal  1000)  ;; 6 
```

Enter fullscreen mode Exit fullscreen mode

接下来，接受一个数字并检查有效性的函数可以写成如下形式:

```
(defn  is-valid?  [number]  (let  [first-four  (quot  number  10)  check-sum  (rem  number  10)  verify  (sum-octal  first-four)]  (=  verify  check-sum))) 
```

Enter fullscreen mode Exit fullscreen mode

```
user>  (is-valid?  10006)  ;; true  user>  (is-valid?  99998)  ;; false 
```

Enter fullscreen mode Exit fullscreen mode

`is-valid?`函数和`#(Integer/parseInt %)`可以组合在一起

```
(comp  is-valid?  #(Integer/parseInt  %))  user>  ((comp  is-valid?  #(Integer/parseInt  %))  "10006")  ;; true 
```

Enter fullscreen mode Exit fullscreen mode

最后，`validate-scratch-card`函数将形成如下

```
(defn  validate-scratch-card  [card-number]  (let  [f  (comp  is-valid?  #(Integer/parseInt  %))]  (->>  (string/split  card-number  #"(-|\s)")  (map  f)  (every?  true?)))) 
```

Enter fullscreen mode Exit fullscreen mode

```
user>  (validate-scratch-card  "10006 12342 00081 99993")  ;; true  user>  (validate-scratch-card  "10006 12342 00081 99998")  ;; false 
```

Enter fullscreen mode Exit fullscreen mode