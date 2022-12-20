# 不太基本的压缩:霍夫曼编码

> 原文：<https://dev.to/mellen/less-basic-compression-huffman-coding-23gp>

作为对我上一篇文章的回应，我得到了一些好的建议:

[![sudhan96 profile image](img/78e92b39d9710e0cddedd073d32b82c8.png) ](/sudhan96) [ raja sudhan ](/sudhan96) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/sudhan96) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/droidraja) [<time datetime="2018-10-18T04:05:00Z">Oct 18 '18</time>](/sudhan96/comment/683i)

很好的文章，看看霍夫曼编码，它对二进制文件做了同样的事情，特定的算法是他在学生时代发明的。

所以我跑去维基百科阅读了一遍。

我花了一段时间，当然比我的简单努力花了一个下午的时间还要长，来理解压缩和解压缩算法。我边走边写代码，最后我想出了一个可行的概念证明。(和上次在同一个回购里。):

## ![GitHub logo](img/04febf72106d337f3956ebfaf9121dae.png) [梅伦](https://github.com/Mellen) / [文件 _ 压缩](https://github.com/Mellen/file_compression)

### 用于压缩文件的 python 3 程序

<article class="markdown-body entry-content" itemprop="text">

# 文件压缩

探索如何压缩文件。

tc.py 使用了我对损耗压缩应该如何工作的基本理解(见[https://dev.to/mellen/the-basic-basics-of-compression-lap](https://dev.to/mellen/the-basic-basics-of-compression-lap)来看看我是如何想出它的)。)

py 是 Huffman 编码的一个实现，适用于任何类型的文件。与我的基本实现相比，它在压缩方面要好得多。

(**需要 python 3** )

py 是一个文本文件压缩程序

```
 usage: tc.py [-h] [-d] file name

 positional arguments:  
    file name   The name of the file to be processed.

 optional arguments:  
    -h, --help  show this help message and exit  
    -d          Decompress the file (defaults to compressing). 
```

py 是一个适用于任何类型文件的压缩程序

```
 usage: bc.py [-h] [-d] file name

 positional arguments:  
    file name   The name of the file to be processed.

 optional arguments:  
    -h, --help  show this help message and exit  
    -d          Decompress the file (defaults to compressing). 
```

</article>

[View on GitHub](https://github.com/Mellen/file_compression)

如果只是想跳转到代码中，文件`compressor.py`、`decompressor.py`、`binarytree.py`中有重要的代码，具体来说就是 [`BinaryCompressor`](https://github.com/Mellen/file_compression/blob/master/compressor.py#L54) 、 [`BinaryDecompressor`](https://github.com/Mellen/file_compression/blob/masteAr/decompressor.py#L44) 、 [`Leaf`](https://github.com/Mellen/file_compression/blob/master/binarytree.py#L4) 、 [`Node`](https://github.com/Mellen/file_compression/blob/master/binarytree.py#L20) 、 [`buildTree`。](https://github.com/Mellen/file_compression/blob/master/binarytree.py#L42)

关于 Huffman 编码的每篇文章首先要讨论的是构建一棵二叉树。我认为解释二叉树不在这篇文章的范围之内(因为我不是这方面的专家)，所以[维基百科](https://en.wikipedia.org/wiki/Binary_tree)就足够了。作为一个简短的总结，霍夫曼编码中的树结构看起来很像家谱树，但它是反向构建的，从子代到祖先。

霍夫曼编码是建立在这样一个理念上的:一个符号在一个文本中出现得越频繁，它所占的空间就越小。所以压缩文件的第一步是找出每个字节的频率。

```
def getFrequencies(self, all_bytes):
    byte_set = set(all_bytes)

    bytes_frequency_dict = {b:0 for b in byte_set}

    for b in all_bytes:
        bytes_frequency_dict[b] = bytes_frequency_dict[b] + 1

    return sorted([item for item in bytes_frequency_dict.items()], key=lambda item:item[1]) 
```

这是`BinaryCompressor`类的一部分。它从文件中获取一个字节列表，从中创建一个`set`,并将其转换为一个字节值列表和它们各自的频率。

事实上，如果使用预先定义的字节到频率的映射，可以跳过这一步。如果你有一个有限的域，你可以选择一组预定义的频率，举个(愚蠢的)例子，如果你正在压缩一个英文拼字板，你的频率是`('E',12), ('A',9), ('I',9), ('O',8), ('N',6), ('R',6), ('T',6), ('L',4), ('S',4), ('U',4), ('D',4), ('G',3), ('B',2), ('C',2), ('M',2), ('P',2),('F',2), ('H',2), ('V',2), ('W',2), ('Y',2),('K',1),('J',1), ('X',1), ('Q',1), ('Z',1)`。预定义映射的好处是，您不必将映射存储在压缩文件中，它会被硬编码到算法中。然而，我希望该算法适用于广泛的文件，所以它生成每个文件的频率。

现在我们有了频率，我们该怎么做呢？如果你读了上一篇文章，或者查阅了存储库的 git 日志，你可能会知道，我以前曾经玩弄过频率和文本压缩，但是我想不出如何以可解压缩的形式对数据进行编码。这就是这棵树的用途。

```
def buildTree(bytes_frequency):
    tree = [Leaf(bf, bf[1]) for bf in bytes_frequency]

    leaves = []

    while len(tree) > 1:
        left, right = tree[:2]
        if type(left) is Leaf:
            leaves.append(left)
        if type(right) is Leaf:
            leaves.append(right)
        tree = tree[2:]
        node = Node(left, right, left.value + right.value)
        tree.append(node)
        tree = sorted(tree, key=lambda node: node.value)

    return leaves, tree[0] 
```

这基本上是维基百科文章中描述的算法。该树开始时是一组按频率(升序)排序的叶子。第一对叶子从列表中删除并加入一个`Node`，其值成为叶子值的总和。然后将新节点添加到列表中，并对列表进行排序。重复这一过程，直到列表中只剩下一个节点。这是根节点。除了根节点之外，该函数还返回一个树叶列表。这是为了更容易创建字节→码字映射。

`Node`和`Leaf`类有一个`code`成员，它存储`Leaf`的代码字或`Node`中代码字的进程。一个字节的代码字是反向构建的，通过在树的分支中的每一个`Node`的代码被更新为最后的特定`Leaf`来完成`Node`的构建。左侧节点用“1”更新，右侧节点用“0”更新。

```
class Leaf():
    def __init__(self, ...):
        ...
        self.code = ''

    def update_code(self, update):
        self.code = update + self.code

class Node()
    def __init__(self, left, right, value):
        self.value = value
        self.left = left
        self.right = right
        self.code = ''

        self.left.update_code(LEFT)
        self.right.update_code(RIGHT)

    def update_code(self, update):
        self.code = update + code
        self.left.update_code(update)
        self.right.update_code(update) 
```

所以现在树建好了，码字也建立了。编码(即压缩)是用它们的码字替换字节的情况。

```
symbol_map = {leaf.data[0]:leaf.code for leaf in leaves}

output_bits = '1'
for b in all_bytes:
    output_bits = output_bits + symbol_map[b] 
```

如果计算的位以 0 开始，则这些位用“1”初始化，这样在转换为整数时不会丢失任何信息。从这里，这些位被转换成一个整数。

```
byte_count = (len(output_bits)+7) // 8

output_int = int(output_bits, 2)

output_bytes = output_int.to_bytes(byte_count, sys.byteorder) 
```

在将这些字节写入文件之前，会创建一个头来存储字节集及其频率。我允许频率的最大值是可变的，以防我压缩一个非常大的文件。因此，前 2 个字节是唯一字节的数量，然后接下来的 8 个是频率的大小，其余的是每个字节/频率对。

```
max_count_bytes = ceil(leaves[-1].data[1].bit_length()/8)

header_bytes = len(leaves).to_bytes(2, sys.byteorder)
header_bytes += max_count_bytes.to_bytes(8, sys.byteorder)

for leaf in leaves:
    header_bytes += (leaf.data[0].to_bytes(1, sys.byteorder))
    header_bytes += leaf.data[1].to_bytes(max_count_bytes, sys.byteorder)

with open(self.outputname, 'wb') as out_file:
    out_file.write(header_bytes)
    out_file.write(output_bytes) 
```

压缩完成了。

解压缩以类似的方式开始，通过构建树。但是这次频率是从文件中读取的，而不是计算出来的。

```
byte_frequencies = []
input_bytes = None
with open(self.filename, 'rb') as input_file:
    leaves_count = int.from_bytes(input_file.read(2), sys.byteorder)
    max_count_bytes = int.from_bytes(input_file.read(8), sys.byteorder)
    while leaves_count > 0:
        b = input_file.read(1)
        c = int.from_bytes(input_file.read(max_count_bytes), sys.byteorder)
        byte_frequencies.append((b,c))
        leaves_count -= 1
    input_bytes = input_file.read()
_, tree = buildTree(byte_frequencies) 
```

接下来，输入的字节被变回一个大整数，记住为了安全起见，要去掉我们添加的第一位。

```
input_bits = bin(int.from_bytes(input_bytes, sys.byteorder))[3:] 
```

最后，通过重复遍历树并累积找到的字节，重新创建原始文件。通过从根开始遍历树，如果当前位是‘1’，则采用左分支，或者如果该位是‘0’，则采用右分支。当找到一个`Leaf`时，从其中获取字节值并将其添加到列表中，然后将搜索重置为根。

一旦所有的位都被读取，写出文件。

```
output_bytes = b''
current_node = tree
for bit in input_bits:
    if bit == LEFT:
        current_node = current_node.left
    else:
        current_node = current_node.right

    if type(current_node) is Leaf:
        output_bytes += current_node.data[0]
        current_node = tree

with open(self.outputname, 'wb') as output_file:
    output_file.write(output_bytes) 
```

与我提出的算法相比，这个算法的效果明显更好。例如，压缩 compressor.py:

| 压缩类型 | 文件大小(字节) |
| --- | --- |
| 无压缩 | Three thousand four hundred and fifty-four |
| 我的算法 | Two thousand eight hundred and fifty-four |
| 霍夫曼编码 | Two thousand and ninety-five |

虽然我开始时很沮丧，试图理解算法，但最后当它工作时，我有一种很好的满足感。我会对代码中的一些东西进行修改，进行清理，保持命名的一致性，并删除一些不使用的东西。我还想改变代码的工作方式，让文件流入，能够处理任意大小的文件，不受可用内存大小的限制。

感谢阅读！