# 对 Go 成功的反驳预示着 Rust 的成功

> 原文：<https://dev.to/jrwren/a-rebuttal-to-the-success-of-go-heralds-that-of-rust-3p1f>

乔治写道，铁锈在各方面都更优越。去这里读吧:[https://medium . com/@ George 3d 6/the-success-of-go-herald-that-the-rust-73c B2 E4 c 0500](https://medium.com/@george3d6/the-success-of-go-heralds-that-of-rust-73cb2e4c0500)
这听起来可能有些防御性。我为我糟糕的写作道歉。相反，我想说的是，整个 OP 都是从错误的心态写出来的，以下几点是这种错误心态的具体例子。
这篇文章忽略了编写 Go 的首要原因:编译速度！这篇文章还忽略了写 Go 的另一个非常重要的原因:它是为程序员而写的，“不能理解一种优秀的语言，但我们想用它们来构建好的软件”。这句话出自罗布·派克。
这篇文章强调了不可变类型的重要性，普通程序员知道如何利用不可变类型从中获益吗？
“Rust 的标准库和 go 一样丰富”——真的？net/http 在哪里？仅仅这一点就使这一声明成为彻头彻尾的谎言。找 json，xml？同样不在 stdlib 中。像 tar，zip，bz2，lzw，gzip 这样的压缩和归档？还是在 Go stdlib 中，不是在 Rust std 中。密码术包括对称、非对称和散列？在 Go stdlib 和 Rust std 中缺席。我可以继续，但是我必须参考下面的 go 标准库。自己比较一下[https://golang.org/pkg/](https://golang.org/pkg/)和[https://doc.rust-lang.org/stable/std/](https://doc.rust-lang.org/stable/std/)。
“Rust 的软件包生态系统胜过…”也许，但不是在一些重要的方面，考虑一下[https://github.com/search?q=language%3ARust+stars%3A%3E1000&type = Repositories](https://github.com/search?q=language%3ARust+stars%3A%3E1000&type=Repositories)vs .[https://github.com/search?l=&p = 1&q = language % 3A Go+stars % 3A % 3e 1000&ref = advsearch&type = Repositories&utf8 =](https://github.com/search?l=&p=1&q=language%3AGo+stars%3A%3E1000&ref=advsearch&type=Repositories&utf8=%E2%9C%93)
“我认为我们可以称 Rust 为一种优越的语言，毫不夸张地说，每一种可能的方式都是如此。”不是编译时的速度。对于一般和一般以下的开发者来说不容易使用。当这两点是你最重要的价值观时，Rust 看起来一点都不优越。
关于过临界点，做中流砥柱，我绝对同意。铁锈会留下来，我很高兴它会留下来。对于大多数任务来说，它是比其他语言更好的语言，我绝对不同意。Rust 的位置是取代 C++。当需要这种级别的控制时，它是一种比 C++更简单、更理智的语言，可以在同样的地方使用。对于其他任何东西，一种更简单的语言，需要更少的精神负担和更快的编译时间，更适合这个任务。
最后，在心态和观点上，如果“优越”一点都不考虑人的方面，这篇帖子可能有很多道理，但是，代码是由人类编写的。人类有着不同的需求，而不仅仅是一系列受支持的特性。记住 Go 编程语言在编写时的目标(摘自 Rob Pikes 2009 Google Tech Talks 演示文稿):类型安全、内存安全、对并发性的良好支持、GC 和编译速度。随着项目的增长，编译速度变得很重要，这一点很容易被忘记。许多语言已经解决了所有这些问题，除了最后一个。Go 继续强调这一点。在 1.4 版本中，编译速度大大降低，但在接下来的几个版本中，编译速度大大提高，直到比以前更快。这是开发人员生产力的一个重要原则。如果我们不再重视这一点，那么围棋最重要的部分之一就不会受到重视。如果你不重视它，那么你必须说出来。毕竟，它是语言中最重要的部分之一。
继续心态和观点:这篇文章不重视围棋的简单性。这也是围棋最大的优势之一。毫无疑问，Rust 中的泛型和内存管理使它比 Go 更复杂。Go 的简单性是如此强大，以至于许多开发人员不想在语言中使用泛型。他们不想增加复杂性。忽略这种简单性作为一种价值观，就是忽略了语言中最重要的一部分。考虑到我们在比较事物时必须重视的这些额外的东西，这篇文章中的结论就没那么简单了。是的，有生锈的地方。还有一个围棋的地方。用 Go 写的东西应该像文章建议的那样用 Rust 写吗？绝对不行。