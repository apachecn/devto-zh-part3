# Ruby 中的 leet code:107。二叉树层次顺序遍历 2

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-107-binary-tree-level-order-traversal-ii-2mbo>

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

  result.reverse!
end 
```

这与问题 102 的迭代解法非常相似，除了我们在最后反转了`result`。有关详细解释，请阅读:

[![algobot76 image](img/a84a2d73a3e166ffd1b4212434c2100a.png)](/algobot76) [## Ruby 中的 leet code:102。二叉树层次顺序遍历

### 谢 2 月 28 日 191 分钟阅读

#leetcode #ruby #binarytree](/algobot76/leetcode-in-ruby-102-binary-tree-level-order-traversal-2e59)