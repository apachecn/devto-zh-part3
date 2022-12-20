# LeetCode 98。验证二叉查找树

> 原文：<https://dev.to/algobot76/leetcode-98-validate-binary-search-tree-4o29>

# 递归

```
public class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValid(root, null, null);
    }

    private boolean isValid(TreeNode p, Integer low, Integer high) {
        if (p == null) {
            return true;
        }

        return (low == null || p.val > low) && (high == null || p.val < high) && isValid(p.left, low, p.val) && isValid(p.right, p.val, high);
    }
} 
```

在 BST 中，左边的子节点小于其父节点，而右边的子节点总是大于其父节点。解决方案就是基于这个属性。在`isValid`函数中，我们递归地检查一个节点的值是否在`low`和`high`之间。存在一种边缘情况，其中节点可能具有等于`Integer.MIN_VALUE`或`Integer.MAX_VALUE`的值。所以我们用`null`来表示无穷大。

*时间复杂度* : `O(n)`

*额外空间* : `O(1)`

# 按序遍历

```
public class Solution {
    private TreeNode prev;

    public boolean isValidBST(TreeNode root) {
        prev = null;
        return isMonotonicIncreasing(root);
    }

    private boolean isMonotonicIncreasing(TreeNode p) {
        if (p == null) {
            return true;
        }

        if (isMonotonicIncreasing(p.left)) {
            if (prev != null && p.val <= prev.val) {
                return false;
            }
            prev = p;
            return isMonotonicIncreasing(p.right);
        }

        return false;
    }
} 
```

如果树是一个有效的 BST，它的元素应该严格遵循有序遍历中的递增顺序。我们可以利用这个性质来解决问题。要了解更多关于有序遍历的内容，请查看这个[链接](https://en.wikipedia.org/wiki/Tree_traversal#In-order_(LNR))。我们定义`prev`来跟踪有序遍历中的前一个元素。每当我们检测到存在一个比`prev`小的树节点`p`，我们就知道这棵树不是一个有效的 BST。

*时间复杂度* : `O(n)`

*额外空间* : `O(1)`