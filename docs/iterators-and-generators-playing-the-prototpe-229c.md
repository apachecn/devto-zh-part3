# 迭代器和生成器——扮演原型

> 原文：<https://dev.to/zanehannanau/iterators-and-generators-playing-the-prototpe-229c>

# 打原型

嗯，也许是吧。

迭代器和生成器并不是新概念。修改原型也不是。但很少两者都做到。

所以，让我们开始吧！

我这里用的是 typescript 但它的写作方式会是 js 中更好的垫片。

```
let prot = ((function*(){}).prototype as unknown as any).__proto__ as any 
```

它很丑，但它给了我们全球发电机的原型。用其他方式来断言这一点可能会令人生厌。

```
for (let fn of gens) Object.defineProperty(prot, fn.name, {value: fn}) 
```

…是的，用发电机来发电。好像就那么简单…

```
let gens = [
  function* map<U, T>(this: IterableIterator<U>, cb: (value: U, iteration: number) => T): IterableIterator<T> {
    let iteration = 0
    for (const value of this) yield cb(value, iteration++)
  },
  function* take<T>(this: IterableIterator<T>, num: number): IterableIterator<T> {
    if (num < 1) return;
    for (const t of this) {
      yield t;
      if (--num < 1) break
    }
  },
  function* takeWhile<T>(this: IterableIterator<T>, condition: (value: T, iteration: number) => boolean): IterableIterator<T> {
    let iteration = 0
    for (const t of this) {
      if (condition(t, iteration++)) yield t
      else break
    }
  },
  function* filter<U, T = Exclude<"" | 0 | null | undefined, U>>(this: IterableIterator<U>, cb: (value: U, iteration: number) => boolean = v => !!v): IterableIterator<T> {
    let iteration = 0
    for (const value of this) if (cb(value, iteration++)) yield value as unknown as T
  },
  function fold<U, T>(this: IterableIterator<U>, init: T, cb: (acc: T, curr: U, iteration: number) => T) {
    let iteration = 0
    for (const c of this) init = cb(init, c, iteration++)
    return init
  },
  // Iterates until both are complete.
  function* zip<A, B>(this: IterableIterator<A>, other: IterableIterator<B>): IterableIterator<[A, B]> {
    try {
      let a = this.next(), b = other.next()
      while (!(a.done || b.done)) {
        yield [a.value, b.value]
        a = this.next()
        b = this.next()
      }
    } finally {
      this.return()
      other.return()
    }
  },
  function* enumerate<T>(this: IterableIterator<T>): IterableIterator<[number, T]> {
    let iteration = 0
    for (const t of this) yield [iteration++, t]
  },
  function unzip<A, B>(this: IterableIterator<[A | undefined, B | undefined]>): [A[], B[]] {
    let a = [], b = []
    for (const [aa, bb] of this) {
      if (aa !== undefined) a.push(aa);
      if (bb !== undefined) b.push(bb)
    }
    return [a, b]
  },
  function collect<T>(this: IterableIterator<T>): T[] {
    return Array.from(this)
  }
] 
```

所有这些都基于 rust (13.2 MiB)的 [`std::iter::Iterator`，并且都只使用原生 js 函数。现在你可以写你的无限循环，让它懒洋洋地走…我想。](https://doc.rust-lang.org/std/iter/trait.Iterator.html)

```
function* idgen(i = 0) {while(1) yield i++}
for (let value of idgen().filter(v => v % 3 == 0).map(v => v * v).take(32)) console.log(value)
0
9
36
81
144
225
324
441
576
729
900
1089
1296
1521
1764
2025
2304
2601
2916
3249
3600
3969
4356
4761
5184
5625
6084
6561
7056
7569
8100
8649 
```