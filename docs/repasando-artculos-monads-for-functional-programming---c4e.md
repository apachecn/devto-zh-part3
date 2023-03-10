# Repasando Artículos:函数式编程的单子

> 原文：<https://dev.to/gustavo94/repasando-artculos-monads-for-functional-programming---c4e>

[瓦德勒，菲利普，2001 年](http://roman-dushkin.narod.ru/files/fp__philip_wadler_001.pdf)

功能编程与数学密切相关，函数的定义并不是唯一的情况，这种关系的另一个例子是抽象数学中类别理论产生的 monada 概念。这一概念被纳入了方案编制，以解决纯功能语言的问题，因为它试图做一些在强制性语言中相对简单的事情，例如处理异常情况、维护计数器或创建执行跟踪。

文章提到了这些问题是如何由于功能语言中的明确数据流而出现的:“纯功能语言具有这一优势:所有数据流都是显式的。而这种缺点:有时是痛苦地显而易见的。”但文章也强调了这种明确的数据流所提供的模块性，这有助于强调 monada 概念。

monada 也称为标准构造，简单地说是一种数据结构，它使我们能够丰富函数的返回，同时通过封装某些在强制语言中被视为副作用的行为来促进函数的组成。了解 monadas 的一个关键示例是 scala 或 vavr(Java 功能库)中的 Try 结构，该结构允许存储异常，因此 Try 对象可以存储整数或异常，从而使您可以在不丢失异常控制的情况下通过函数组合来设置数据流，而无需处理异常

以下是使用 vavr try 类在函数合成过程中捕获异常的示例:

```
 public static Try<Integer> dividir(Integer dividendo, Integer divisor) {
      return Try.of(() -> dividendo/divisor);
  }

  public static void main(String[] args) {
      Try<Integer> resultado = dividir(100, 0).flatMap(r1 -> dividir(r1, 10));
      System.out.println("RESULTADO 1 = " + resultado.map(String::valueOf).getOrElseGet(Throwable::getMessage));// RESULTADO 1 = / by zero

      resultado = dividir(100, 10).flatMap(r1 -> dividir(r1, 10));
      System.out.println("RESULTADO 2 = " + resultado.map(String::valueOf).getOrElseGet(Throwable::getMessage));// RESULTADO 2 = 1

  } 
```

## 僧侣法

monada 的正式定义包括三项必须在这些结构中遵守的法律，尽管这些法律可能具有更高的理论价值而不是实际价值。

#### 【左单位】o【左身份】

*我找不到西班牙文译文*

该法规定，应用将 moneda 返回到任何值的函数必须等于使用 flatMap 合成函数将该函数应用到 moneda 中的值。

```
 public static void main(String[] args) {
      Integer cien = 100;
      Try<Integer> tryCien = Try.of(() -> cien);
      Boolean r = tryCien.flatMap(valor -> dividir(100, valor)   ).equals(  dividir(100, cien));// True
  } 
```

```
 return a >>= f = f 
```

#### 【权利单位】o【权利身份】

*我找不到西班牙文译文*

与上述法律类似，如果我们有函数接收值并返回相同的值，但在 monada 中，我们必须能够在平面图中或直接对值应用此函数，并获得相同的结果。

```
 public static void main(String[] args) {
      Integer cien = 100;
      Try<Integer> tryCien = Try.of(() -> cien);
      // El método success retorna el valor en un try sin hacerle ningún cambio
      Boolean r = tryCien.flatMap(valor -> Try.success(valor)).equals(  Try.success(cien));// True
  } 
```

```
 m >>= return = m 
```

JAVA 中的上述例子给人的错误印象是，这两种法律是相同的，只是前一种法律的一个具体特点，但正如我所说的那样，这些法律在理论上更有价值，其区别在 haskell 等更为数学化的语言中更为明显。

#### Asociatividad o“结合性”

该法提到僧侣按顺序或通过相互封闭的方式应用 flatMap 方法的能力。

```
 public class PruebaMonadas {

  public static Try<Integer> dividirPor10(Integer dividendo) {
      return Try.of(() -> dividendo/10);
  }

  public static Try<Integer> duplicar(Integer n1) {
      return Try.of(() -> n1 * 2);
  }

  public static void main(String[] args) {

      Try<Integer> tryCien = Try.of(() -> 100);
      Try<Integer> r1 = tryCien.flatMap(PruebaMonadas::dividirPor10).flatMap(PruebaMonadas::duplicar);
      Try<Integer> r2 = tryCien.flatMap(cien -> dividirPor10(cien).flatMap(PruebaMonadas::duplicar));
      // El método success retorna el valor en un try sin hacerle ningún cambio
      Boolean r = r1.equals(r2);// True

  }

} 
```

```
 (m >>= f) >>= g ≡ m >>= (\x -> f x >>= g) 
```