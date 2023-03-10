# Git:基于内容的寻址、分支和标签

> 原文：<https://dev.to/kabisasoftware/git-content-based-addressing-branches-and-tags-f5j>

在本系列的第一部分中，我们查看了对象数据库。我们了解到提交是一种对象类型，就像斑点、树和标签一样。提交引用树，而树引用其他树和 blobs。

我们还了解到对象是通过散列来引用的，但是我们对这些散列了解得不多。让我们看看这些哈希值是如何确定的，以及为什么它很重要。

## 基于内容的寻址

那么这个哈希是怎么确定的呢？Git 使用一种叫做基于内容寻址的技术。对象的内容(使用`git cat-file -p`时看到的东西)是决定 hash 的唯一因素。这适用于所有类型的对象:blobs、树、提交和标记。

这有两个原因:存储库的大小和性能。考虑一下提交是如何包含对树的引用的，并且这个树包含提交时存储库的完整目录结构。提交本质上是存储库的快照。如果我们有数千个提交，这意味着我们有数千个存储库的快照。这些历史应该占了很大篇幅吧？

基于内容的寻址在这方面很有帮助。让我们看看当我们试图向我们在第一部分中创建的存储库添加一个新文件时会发生什么:

```
$ echo 'exit' > script.sh
$ git add script.sh
$ git commit -m 'Add script'
[master 5b5f50a] Add script
 1 file changed, 1 insertion(+)
  create mode 100644 script.sh 
```

我们可以用`git cat-file`来看看树 <sup id="fnref1">[1](#fn1)</sup> :
的内容

```
$ git cat-file -p 5b5f50a^{tree}
100644 blob a5c19667710254f835085b99726e523457150e03    README
100644 blob a3abe50906e1a7234d71453aaa367b0f8d7a9c2d    script.sh 
```

正如所料，我们看到新添加的文件也被这个树引用。但是看一下第一个条目，并将其与之前提交的树进行比较:

```
$ git cat-file -p head~^{tree}
100644 blob a5c19667710254f835085b99726e523457150e03    README 
```

它引用完全相同的对象。由于自述文件的内容没有改变，其地址也没有改变。树也有同样的属性。如果树中的文件都没有改变，则树的内容没有改变，并且树将具有相同的散列。如果对象具有引用它们的相同散列，则对象只需存储一次。

我们可以把它形象化，让它变得更清楚。在此图中，顶部提交是最新的，它引用其父级。两个提交都有自己的树，但是两个树都指向 README 文件的完全相同的 blob。即使第二次提交重命名了 README 文件，树也会有所不同，但它仍然指向同一个 blob。

[![](img/8fda6ca8378c4440e049a24d68c69498.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gFK50v1W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/TCmKV2A.png)

另一个有趣的例子是，如果我们将自述文件移到 docs 文件夹中会发生什么。

```
$ mkdir docs
$ mv README docs/README
$ git commit -a -m 'Move README into docs'
[master c9ab7dd] Move README into docs
 Date: Tue May 7 12:10:39 2019 +0200
 1 file changed, 0 insertions(+), 0 deletions(-)
 rename README => docs/README (100%) 
```

[![](img/0b07d4a817f2af9607f94f0ca8411e65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bsn2hanH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7kSlRND.png)

在这次提交中，docs 文件夹与第一次提交时的根文件夹完全相同。已经为新提交的根文件夹创建了新的树，但是原始提交的树已经被重用。

通过使用基于内容的寻址，Git 只需要存储已经改变的文件和树。这些成千上万的提交和成千上万的快照现在占用的空间少了很多。在每次提交时，我们只改变代码库的一小部分。

基于内容的寻址的第二个优点是当你分两次提交时的性能。假设我们用下面的树提交:

```
040000 tree 356a192b7913b04c54574d18c28d46e6395428ab    huge-directory
100644 blob da4b9237bacccdf19c0760cab7aec4a8359010b0    small-file 
```

`huge-directory`有很多子文件夹和很多文件。另一个提交具有以下树:

```
040000 tree 356a192b7913b04c54574d18c28d46e6395428ab    huge-directory
100644 blob 77de68daecd823babbb58edb1c8e14d7106e83bb    small-file 
```

如果我们对这两个提交进行区分，查看存储在`huge-directory`中每个文件将会花费很多时间。幸运的是，我们不需要这么做。如果你查看两棵树中`huge-directory`的 SHA，它们是相同的。这一定意味着它们包含相同的内容。因此，我们唯一需要区别的是`small-file`。

## 分支无分支

对 Git 来说，一个重要的概念是分支。分支对于任何版本控制系统来说都是非常重要的，没有分支就无法做到这一点，对吗？事实证明，你可以。使用提交完全可以进行分支，只是不太方便。

让我们再看一下我们的示例存储库。我们现在有两个提交:初始提交和添加`script.sh`的提交。我们将创建两次提交，但是我们将重置回中间的第二次提交。

```
$ echo 'Hello, world!' > README
$ git commit -a -m 'Shout in README'
[master 3bf75bf] Shout in README
 1 file changed, 1 insertion(+), 1 deletion(-)
$ git reset --hard head~
HEAD is now at 5b5f50a Add script
$ echo 'Hello, world?' > README
$ git commit -a -m 'Question in README'
[master 00e9f1c] Question in README
 1 file changed, 1 insertion(+), 1 deletion(-) 
```

我们将直接合并两个提交，修复我们刚刚创建的合并冲突:

```
$ git merge 3bf75bf
Auto-merging README
CONFLICT (content): Merge conflict in README
Automatic merge failed; fix conflicts and then commit the result
$ echo 'Hello, world!?' > README
$ git commit -a --no-edit
[master ec9c76d] Merge commit '3bf75bf' 
```

所以现在我们已经创建了三个新的提交，两个简单的更改和一个合并提交。合并提交还有一个包含合并后存储库状态的树，包括合并冲突的解决。

这是它在视觉上的样子。为了使图形更简单，不再显示树和斑点。

[![](img/e7ac96fe1ccf09f230ff112cfdfe41be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZlPbXLDx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ow9TDiE.png)

同样，顶部提交是最新的。现在有两个以`5b5f50ad`为父的提交。这是分支发生的地方，从现在开始有两个不同的历史。最近一次提交，`ec9c76d3`合并了这两个分支，所以现在在`00e9f1c4`和`3bf75bf3`中所做的更改都被应用了。

这就是分支的本质。请注意，这张图片中完全没有分支。事实上，为了达到这一点，我们没有创建新的分支。我们唯一的分支是 master。

## 分支和标签加什么？

如果我们可以在没有分支的情况下进行分支，这些分支增加了什么？到目前为止，我们已经通过 SHA 引用了每个提交。虽然这可以工作，但是即使对于这样一个小的存储库来说也不是很方便。一旦我们有数百甚至数千个提交，就不可能跟踪所有的提交。

这就是我们可以使用分支和标记的目的:有一个提交的简单引用。在内部，它们只不过是对提交的引用。让我们来看看大师分支是什么样子的。还记得我们在第一部分中讨论的那个`.git`目录吗？树枝也存放在那里。我们可以在`.git/refs/heads`中找到所有的分支，到目前为止它只包含一个`master`文件。这个文件的内容是:

```
$ cat .git/refs/heads/master
ec9c76d3b3eaea9b3ac57e1ac74ddd5189267341 
```

这是最近提交的 SHA。所以每个分支只引用它最近的提交，也称为它的头。如果您当前在分支 <sup id="fnref2">[2](#fn2)</sup> 上，并且您创建了一个新的提交，`.git/refs/heads`中的分支文件也将被更新以指向新的提交。

我们将创建一个新的分支，并在该分支上创建一个提交。这是那个的结果:

```
$ git checkout -b my-awesome-branch
Switched to a new branch 'my-awesome-branch'
$ echo 'Awesome Hello World!?' > README
$ git commit -a -m 'Awesome commit'
[my-awesome-branch 3cf54d4] Awesome commit
 1 file changed, 1 insertion(+), 1 deletion(-) 
```

[![](img/47c6fd0cda064519fa8eed4ed975fb85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZYD9pXZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/wbhcEh2.png)

标签与分支非常相似。唯一的区别是，当您签出一个分支时，git 会记住这一点，并在您创建新的提交时移动该分支，而签出一个标记仅仅意味着签出该标记引用的提交。一个标签永远不会改变，除非你明确地告诉 Git 这样做。

Git 区分两种类型的标签:带注释的标签和轻量级标签。

轻量级标签只是对提交的引用，就像分支一样。它们存储在`.git/refs/tags`目录中，就像分支一样，文件名是标签的名称，文件包含提交的阿沙 <sup id="fnref3">[3](#fn3)</sup> 。

带注释的标签是 Git 创建的实际对象，它携带一些信息。例如，它有一个消息、标签和标签日期。因为这些对象不容易找到，例如当您运行`git tag`时，一个指向您的注释标签的轻量级标签也被创建。

这是两种标记形式的外观:

```
$ git tag my-lightweight-tag
$ cat .git/refs/tags/my-lightweight-tag
ec9c76d3b3eaea9b3ac57e1ac74ddd5189267341 
```

```
$ git tag -a my-annotated-tag -m 'This tag has a message'
$ cat .git/refs/tags/my-annotated-tag
370cba29377db4129a9445c3c309ed596666e20a
$ git cat-file -p 370cba29
object ec9c76d3b3eaea9b3ac57e1ac74ddd5189267341
type commit
tag my-annotated-tag
tagger John Doe <john.doe@example.com> 1557144396 +0200

This tag has a message 
```

当两个标签都被创建时，我们的图表看起来是这样的:

[![](img/a87f65a94cfbf220deacca651a8617f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D-qUsVYQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/up4HB4Z.png)

## 结论

现在我们知道了 Git 的基本原理。在第一部分中，我们学习了 Git 如何存储最基本的数据:提交、blobs 和树。在这一部分中，我们学习了基于内容的寻址如何帮助 Git 高效地存储这些数据。我们还学习了如何使用基本对象进行分支，以及什么是分支和标签。

在本系列的最后一部分，我们将看到这些新知识的一些实际应用。

* * *

1.  我们使用一种语法来直接进入提交树。如果您想了解更多关于如何指定修订的信息，请查看 git-rev-parse 的手册页。 [↩](#fnref1)

2.  这个我们就不细说了，但是“你所在的分支”是存储在`.git/HEAD`里的。通常，它存储一个分支的名称，如`ref: refs/heads/master`。如果您处于无头模式，它包含提交的 SHA。 [↩](#fnref2)

3.  尽管事实上几乎每个标签都指向一个提交(或者一个带注释的标签指向一个提交)，但这并不是绝对必要的。标记也可以指向 blob 和树，所以您可以使用标记来保存对包含您喜欢的任何数据的 blob 的引用。 [↩](#fnref3)