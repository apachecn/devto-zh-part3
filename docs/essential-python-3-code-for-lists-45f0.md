# 列表的基本 Python 3 代码

> 原文：<https://dev.to/mortoray/essential-python-3-code-for-lists-45f0>

我在上一篇文章中列出的所有内容的 Python 3 示例。

[![mortoray image](img/d44da21f46b8ea3a9ef11fbb09c57f91.png)](/mortoray) [## 面试中列表和向量的基本代码

### edA QA mort ora y Mar 1 ' 197min read

#coding #beginners #career #interview](/mortoray/essential-code-for-lists-and-vectors-in-an-interview-5255)

这种语法有很多变体，但是只要你知道一种方法，你就不会有问题。我在大多数情况下试图找到 Pythonic 式的方法。你可以把它作为一个备忘单。

## 基础知识

```
# Create a list mylist = []

# Add an element to the front or back mylist.append( "abc" )
mylist.insert( 0, "def" )

# Pop element off front or back end = mylist.pop()
start = mylist.pop( 0 )

# Forward iterate over elements for item in mylist:
    print( item )

# Get the length of list len( mylist )

# Test if empty if not mylist:
    print( "list is empty" ) 
```

Enter fullscreen mode Exit fullscreen mode

## 基于位置的操作

```
# Get item at location mylist[2]

# Insert an item at location mylist.insert( 3, "abc" ) 

# Remove an item from location del mylist[2]

# Replace/Assign item at location mylist[1] = "def" 
```

Enter fullscreen mode Exit fullscreen mode

## 排序和搜索

```
# Find an item if item in mylist:
    index = mylist.index(item)

# Using `index` and error handling try:
    index = mylist.index( 'abc' )
except ValueError:
    index = None

# Using `next` and filtering next((x for x in mylist if x == 'ghif'), None)

# Find and remove an item if item in mylist:
    mylist.remove( item )

# with error handling try:
    mylist.remove( item )
except ValueError:
    pass

# Find last matching item
# Index of found item, or None next( (index for index in reversed( range( len( mylist ) ) ) if mylist[index] == item), None)

# Alternately, reverse list and use "Find an item", but that copies the list revlist = mylist[::-1]
if item in revlist:
    index = revlist.index( item )

# Sort by natural order
# in-place sort mylist.sort()

# Sort with custom comparator mylist = [ ('a', 10), ('b', 7), ('c',13), ('d',1) ]
# sort by a key (sub-element mylist.sort( key = lambda item: item[1] )
# custom comparator def compare_fn( a, b ):
    return some_cond( a, b )
mylist.sort( key = functools.cmp_to_key( compare_fn ) ) 
```

Enter fullscreen mode Exit fullscreen mode

## 分段操纵

```
# Split the list at arbitrary location tail_of_list = mylist[2:]
head_of_list = mylist[:2]

# Multiple splits based on a match mylist = ['a', 'b', 'c', 'd', 'b', 'e']
[list(y) for x, y in itertools.groupby( mylist, lambda z: z == 'b') if not x]

# Clear the list mylist.clear()

# Remove segment
# delete from position 1 up to, but excluding position 3 del mylist[1:3]

# Concatenate lists mylist + other_list

# Insert list at location
# list slicing replaces the segment of list with another one, here we replace a zero-length slice mylist[1:1] = other_list

# Get a sublist
# sublist starting at position 1 up to, but excluding, position 3 mylist[1:3] 
```

Enter fullscreen mode Exit fullscreen mode

## 更多迭代

```
# Backward for item in reversed( mylist ):
    print( item )

# Partial segment iteration
# using itertools.islice avoids copying the list (which is what would happen if you used a slice) for item in itertools.islice( mylist, 1, 4 ):
    print( item )

# Skipping elements
# step from element 1 to 6 (exclusive) by 2 for item in itertools.islice( mylist, 1, 6, 2 ):
    print( item ) 
```

Enter fullscreen mode Exit fullscreen mode

## 创作

```
# Create from a static list of items mylist = [ 'abc', 'def', 'ghi']

# Create a range of numbers
# a list of numbers from 10..20 (exclusive) numbers = list( range( 10, 20 ) ) 
```

Enter fullscreen mode Exit fullscreen mode

## 数据操作

```
# Mapping [number * 10 for number in numbers]

# Filtering [number for number in numbers if number % 2 == 0]

# Fold / Reduce
# Summing up numbers using builtin add functools.reduce( operator.add, numbers )
# Joining string representations of items functools.reduce( lambda left,right: str( left ) + '/' + str( right ), mylist )

# Zip
# the zip function produces a list of tuples zip( lista, listb )
# to alternate items into one list use reduce functools.reduce( operator.add, zip( lista, listb ) ) 
```

Enter fullscreen mode Exit fullscreen mode

## 高级

```
# Swap elements at two locations mylist[3], mylist[5] = mylist[5], mylist[3]

# Reserve capacity
# Python lists do not expose capacity 
# Replace content in a list mylist[:] = other_list

# Compare two lists lista == listb

# Search a sorted list
# bisect_left/bisect_right work with sorted lists,
# find an item ndx using bisect_left, finds the left-most item ndx = bisect_left( numbers, 4 )
if ndx != len(numbers) and numbers[ndx] == 4
    print( "Found at {}".format(ndx) )

# Iterators
# Manually stepping through an iterator myiter = iter( mylist )
while True:
    try:
        n = next( myiter )
        print(n)
    except StopIteration:
        break

# Multiple iterators at the same time itera = iter( lista )
iterb = iter( listb )
while True:
    try:
        a = next( itera )
        b = next( iterb )
        print( a, b )
    except StopIteration:
        break 
```

Enter fullscreen mode Exit fullscreen mode

* * *

你想学习更多成为伟大程序员的方法吗？看我的书[什么是编程？](http://bit.ly/2tG8fGS)。我看着人，软件存在的原因，软件核心的代码，还有你，键盘后面的人。