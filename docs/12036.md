# Ruby 中的 leet code:102。二叉树层次顺序遍历

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-102-binary-tree-level-order-traversal-2e59>

# 迭代

```
def level_order(root)
  result = []
  return result if root.nil?

  queue = []
  queue << root

  until queue.empty?
    level_size = queue.length
    level = []
    level_size.times do
      node = queue.shift
      level << node.val
      queue << node.left unless node.left.nil?
      queue << node.right unless node.right.nil?
    end
    result << level
  end

  result
end 
```

一般来说，这种方法是遍历给定二叉树的每一层，并将每个节点的`val`加到`result`上。对于每次迭代，我们首先创建一个空的`level`数组，用于存储同一级别节点的值。然后我们将每个节点的`val`添加到`level`数组中，并将下一层的节点推送到`queue`(如果有的话)。在每次迭代结束时，我们将`level`数组推送到`result`。

*时间复杂度* : `O(n)`

*额外内存* : `O(n)`

# 递归

```
def level_order(root, result = [], level = 0)
  return result unless root

  result << [] if result.length == level
  result[level] << root.val
  level_order(root.left, result, level + 1)
  level_order(root.right, result, level + 1)
end 
```

这与前一种方法类似，只是这种方法是使用递归实现的。有趣的是，与迭代方法不同，递归方法不需要一个`queue`来临时存储下一层的节点。

*时间复杂度* : `O(n)`

*额外内存* : `O(1)`