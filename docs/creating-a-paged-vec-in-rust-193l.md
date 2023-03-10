# 在 Rust 中创建“分页”Vec

> 原文：<https://dev.to/kdrakon/creating-a-paged-vec-in-rust-193l>

在这篇文章中，我将向你展示一些我在 Rust 中为给一个`Vec`集合分页而写的代码。我写的 CLI 工具需要它来显示从远程服务器获取的所有向量条目。在大多数情况下，我希望收到很多结果，所以为了在终端中有效地显示它们，我无法合理地呈现所有结果。我决定我将页面的结果。然而，我意识到我还想要以下这些:

*   我想跟踪用户对源向量中元素的选择，**和**，
*   我想在页面上显示用户的选择，以便显示(例如，突出显示他们的光标选择)。

直觉上，这些指数会有所不同。我意识到我需要一个数据结构来回答以下问题:

> "什么元素和元素`n`在同一个页面上，它的索引是什么？"。

我的方法是保留完整的矢量，但一次只显示一页。这个“页面”就是当前索引和用户光标所在的位置。

`PagedVec`获取一个`Vec<A>`并在内部创建一个`Vec<Vec<&A>>`，它从源向量中引用所有的`A`。使用`impl`方法`page_for`，您可以随意获得:

*   a)源向量索引的“页面”,以及
*   b)该“页面”的模数转换索引。

例如:

```
let some_vec = vec![1, 2, 3, 4, 5, 6, 7, 8];
let paged = PagedVec::from(some_vec, 3);

let page_1 = paged.page_for(0); // Some(0, vec![1, 2, 3])
let page_1 = paged.page_for(1); // Some(1, vec![1, 2, 3])
let page_1 = paged.page_for(2); // Some(2, vec![1, 2, 3])

let page_2 = paged.page_for(3); // Some(0, vec![4, 5, 6])

let page_3 = paged.page_for(7); // Some(1, vec![7, 8]) 
```

我最初的实现代码如下👇。真正的魔力来自标准的 libs `chunks`方法；它主要负责页面分区。

```
pub struct PagedVec<'a, A: 'a> {
    page_length: usize,
    pages: Vec<Vec<&'a A>>,
}

impl<'a, A> PagedVec<'a, A> {
    pub fn from(vec: &'a Vec<A>, page_length: usize) -> PagedVec<'a, A> {
        PagedVec {
            page_length,
            pages: vec.chunks(page_length).map(|slice| {
                slice.iter().collect::<Vec<&'a A>>()
            }).collect::<Vec<Vec<&'a A>>>(),
        }
    }

    pub fn page_for(&'a self, index: usize) -> Option<(usize, &'a Vec<&'a A>)> {
        self.pages.get((index as f32 / self.page_length as f32).floor() as usize)
        .map(|page| {
            (
                index % self.page_length,
                page
            )
        })
    }
} 
```

这个实现中我最喜欢的部分:使用 Rust 的[引用和借用](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)功能，我几乎不用额外分配堆就能做到这一点。很简单，所有的“页面”基本上都是指向存在于原始向量中的数据的指针。如果向量的元素是巨大的 T4，那么“页面”就不会复制内存中的数据量。有点像关系数据库中的视图。

在使用这个之后不久，我意识到我可以通过不保留我实际上没有显示的页面来减少我的内存使用。决定使用下面的实现是基于我的 CLI 工具特有的因素的设计选择。

```
pub struct PagedVec<'a, A: 'a> {
    page_length: usize,
    vec: &'a Vec<A>,
}

impl<'a, A> PagedVec<'a, A> {
    pub fn from(vec: &'a Vec<A>, page_length: usize) -> PagedVec<'a, A> {
        PagedVec { page_length, vec }
    }

    pub fn page(&'a self, index: usize) -> Option<(usize, Vec<&'a A>)> {
        let mut paged = self.vec.chunks(self.page_length);
        let opt_page = paged.nth((index as f32 / self.page_length as f32)
          .floor() as usize);

        opt_page.map(|page| (index % self.page_length, page.iter().collect::<Vec<&'a A>>()))
    }
} 
```

除了`chunks`，这里的魔术是`nth`的使用；与从源向量创建所有页面不同，`nth`——结合 Rust [迭代器](https://doc.rust-lang.org/book/ch13-02-iterators.html)的 [*惰性求值*](https://en.wikipedia.org/wiki/Lazy_evaluation)——允许我只创建到所选索引的页面。不要再浪费时间或记忆建立我不需要的页面！😎

有些情况下，使用原始实现将所有页面保存在内存中会更好。哪个更好？这取决于您的用例🤷‍♀️.

**2019 年 3 月 6 日更新**
在分享了我的实现后，社区中的一些 Rust devs 能够指出我代码中的一些改进。

Reddit 用户 [2brainz](https://www.reddit.com/user/2brainz/) 能够分享一个通用的并且完全免费分配的`PagedVec`实现，如下:

```
use std::{cmp::min, marker::PhantomData};

pub struct Paged<'a, T, V> {
    vec: &'a V,
    page_length: usize,
    phantom: PhantomData<&'a T>,
}

impl<'a, T, V> Paged<'a, T, V>
where
    V: AsRef<[T]>,
{
    pub fn new(vec: &'a V, page_length: usize) -> Paged<'a, T, V> {
        Paged {
            vec,
            page_length,
            phantom: PhantomData,
        }
    }

    pub fn page(&self, index: usize) -> Option<(usize, &'a [T])> {
        let slice = self.vec.as_ref();
        let len = slice.len();

        if index < len {
            let page_index = index % self.page_length;
            let start = index - page_index;
            let end = min(len, start + self.page_length);

            slice.get(start..end).map(|s| (page_index, s))
        } else {
            None
        }
    }
} 
```

这里的节省是基于简单地使用支持所提供的`Vec`的切片。

此外，我很惭愧地说，我确实忘记了在任何编程语言中使用浮动类型的一些谬误。许多 Rust devs 提醒我，a)对于数字除法，floor 通常是隐式的，b)float 从来都不精确。为指出[shadow 0133
T2 和](https://www.reddit.com/user/Shadow0133/) [2brainz](https://www.reddit.com/user/2brainz/) 而欢呼。