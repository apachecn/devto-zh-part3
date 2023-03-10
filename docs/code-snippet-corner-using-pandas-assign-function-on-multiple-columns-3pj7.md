# 代码片段角:在多个列上使用 Pandas 的赋值函数

> 原文：<https://dev.to/hackersandslackers/code-snippet-corner-using-pandas-assign-function-on-multiple-columns-3pj7>

[![Code Snippet Corner: Using Pandas' Assign Function on Multiple Columns](img/2e94a64476578387f2b1f7075981bec3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NctWN-Br--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-5.cloudinary.com/hackers-and-slackers/image/upload/f_auto%2Cq_auto/img/codesnippetdatatypes%25402x.jpg)

最近，我不得不找到一种方法来减少熊猫数据帧的内存占用，以便对它进行实际操作。这里有一个方便的窍门！

默认情况下，如果你从一个文件中读取一个数据帧，它会将所有的数字列转换为`float64`类型。这与 Pandas 和 NumPy 背后的哲学是一致的——通过使用严格类型(而不是普通的 Python“duck typing”)，您可以更快地完成事情。`float64`是最灵活的数字类型——它可以处理分数，也可以将缺失值转换成`NaN`。这会让我们把它读入内存，然后开始搞乱它。缺点是消耗大量内存。

现在，假设我们想通过手动将列向下转换为可以处理其值的最小类型来节省内存？而且我们还可以说，我们想变得非常非常懒，不想用手去看一堆数字。假设我们想通过方法链接来实现，因为这里列出了所有的优势:[https://tomaugspurger.github.io/method-chaining](https://tomaugspurger.github.io/method-chaining)

让我们介绍一下我们的示例数据框架。我们将手动将所有值转换成浮点数，因为这是我们从文件中读取时的默认设置。

```
df = pd.DataFrame({
    "stay_float": [0.5, 3.7, 7.5],
    "to_int": [-5, 7, 5],
    "to_uint": [1, 100, 200]}).astype(float) 
```

首先，让我们来介绍一下这个练习的主要部分——Pandas 的`to_numeric`函数，以及它方便的可选参数`downcast`。这将采用一个数字类型- `float`、`integer`(不是`int`)或`unsigned` -然后将其向下转换为可用的最小版本。

接下来，让我们创建一个函数，检查一个列是否可以从浮点数向下转换为整数。

```
def float_to_int(ser):
    try:
        int_ser = ser.astype(int)
        if (ser == int_ser).all():
            return int_ser
        else:
            return ser
    except ValueError:
        return ser 
```

我们在这里使用 try/except 模式，因为如果我们试图将一个带有`NaN`值的列转换成一个整数列，它将抛出一个错误。如果它是一个很好的转换成整数的候选者，我们应该为那些丢失的值计算一个值——但是这对于每一列都是不同的。有时将它设为 0 是有意义的，有时设为列的平均值或中值，或者完全设为其他值。

我还想请大家注意第 4 行，它有一个非常有用的熊猫图案。当您对 Pandas 列执行操作时，比如等于或大于，您会得到一个新列，其中操作是逐个元素应用的。如果你试图建立一个条件，解释器不知道如何处理一个包含`[True, False, True]`的数组——你必须把它归结为一个单一的值。因此，如果您想检查两列是否完全相等，您必须调用`.all()`方法(它有一个有用的兄弟，`any()`)来创建一个条件，该条件实际上可用于控制执行。

接下来，让我们创建一个函数，让我们根据条件对多个列应用转换。`assign`方法非常棒，不离开它会很有趣(或者，如果我们离开它，至少用一个函数替换它，我们可以将它作为到整个数据帧的转换链的一部分)。

```
def multi_assign(df, transform_fn, condition):
    df_to_use = df.copy()

    return (df_to_use
        .assign(
            **{col: transform_fn(df_to_use[col])
               for col in condition(df_to_use)})
           ) 
```

让我们做多重赋值，只要我们创建一个列名和目标值的字典，然后解包它。实际上，跳过这个函数直接使用这个语法会更容易，除了我不知道有什么方法可以在链中访问 DF 列的可过滤列表。我认为熊猫语法的未来版本将包括这一点，因为我听说他们想支持更多的方法链接。就我个人而言，我发现认知负荷的减少是值得的，因为有许多小的模块化乐高积木式的转换链接在一起。

它也是其他小助手功能的良好基础。这是一个把尽可能多的浮点列转换成整数的方法。

```
def all_float_to_int(df):
    df_to_use = df.copy()
    transform_fn = float_to_int
    condition = lambda x: list(x
                    .select_dtypes(include=["float"])
                    .columns)    

    return multi_assign(df_to_use, transform_fn, condition) 
```

看模式在行动！我们决定一个转换函数，我们决定在什么条件下应用所有这些转换(我们可能有一百列，谁想记下所有这些？)，然后我们把它传递给`multi-assign`函数。

```
(df
     .pipe(all_float_to_int)).dtypes

stay_float float64
to_int int64
to_uint int64
dtype: object 
```

酷！但是我们实际上并没有减少数据帧的大小——64 字节的整数占用了 64 字节的浮点，就像一百磅羽毛的重量相当于一百磅砖块的重量。我们所做的是让以后更容易向下转换这些列。

接下来，让我们创建一个函数，该函数获取列的子集，并尝试将其向下转换为尽可能小的版本。我们这里有相当小的值，所以它应该完成一些工作。

```
def downcast_all(df, target_type, inital_type=None):
    #Gotta specify floats, unsigned, or integer
    #If integer, gotta be 'integer', not 'int'
    #Unsigned should look for Ints
    if inital_type is None:
        inital_type = target_type

    df_to_use = df.copy()

    transform_fn = lambda x: pd.to_numeric(x, 
                                downcast=target_type)

    condition = lambda x: list(x
                    .select_dtypes(include=[inital_type])
                    .columns) 

    return multi_assign(df_to_use, transform_fn, condition) 
```

基本模式和以前一样！但是现在我们有两个参数——一个是`target_type`,它告诉我们应该向下转换成什么类型。默认情况下，这将与`initial_type`相同，除了一个例外，我们马上会抓住它！

```
(df
     .pipe(all_float_to_int)
     .pipe(downcast_all, "float")
     .pipe(downcast_all, "integer")
).dtypes

stay_float float32
to_int int8
to_uint int16
dtype: object 
```

好了，现在我们有进展了！不知道我们是否能做得更好？最后一栏有一个显眼的名字！它没有小于 0 的值——如果我们把它存储为一个无符号整数，也许可以节省空间！让我们在链中添加一个管道，尝试将某些整数向下转换为无符号整数...

```
(df
     .pipe(all_float_to_int)
     .pipe(downcast_all, "float")
     .pipe(downcast_all, "integer")
     .pipe(downcast_all,  
           target_type = "unsigned", 
           inital_type = "integer")
).dtypes

stay_float float32
to_int int8
to_uint uint8
dtype: objec 
```

你知道什么，我们可以！

让我们看看这样做会节省多少内存。

```
df.info(memory_usage='deep')

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 3 columns):
stay_float 3 non-null float64
to_int 3 non-null float64
to_uint 3 non-null float64
dtypes: float64(3)
memory usage: 152.0 bytes 
```

vs

```
(df
     .pipe(all_float_to_int)
     .pipe(downcast_all, "float")
     .pipe(downcast_all, "integer")
     .pipe(downcast_all,  
           target_type = "unsigned", 
           inital_type = "integer")
).info(memory_usage='deep')

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 3 columns):
stay_float 3 non-null float32
to_int 3 non-null int8
to_uint 3 non-null uint8
dtypes: float32(1), int8(1), uint8(1)
memory usage: 98.0 bytes 
```

152 降低到 98 -我们降低了 1/3 以上！