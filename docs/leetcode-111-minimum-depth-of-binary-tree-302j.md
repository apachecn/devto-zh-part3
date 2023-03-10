# LeetCode 111。二叉树的最小深度

> 原文：<https://dev.to/algobot76/leetcode-111-minimum-depth-of-binary-tree-302j>

```
public class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }

        Queue<TreeNode> q = new LinkedList<>();
        TreeNode rightMost = root;
        q.add(root);
        int depth = 1;
        while (!q.isEmpty()) {
            TreeNode node = q.poll();
            if (node.left == null && node.right == null) {
                break;
            }
            if (node.left != null) {
                q.add(node.left);
            }
            if (node.right != null) {
                q.add(node.right);
            }
            if (node == rightMost) {
                depth++;
                rightMost = (node.right != null) ? node.right : node.left;
            }
        }

        return depth;
    }
} 
```

我们用 BFS 来解决这个问题。我们将同一级别的节点添加到队列`q`。我们遍历每个节点。当我们遇到一个叶子节点时，我们停下来返回`depth`(最小深度)。在迭代了同一级别的所有节点后，我们仍然找不到叶节点，我们将`depth`增加 1，然后对下一级别的节点重复上述过程。我们也可以使用 DFS。然而，BFS 在高度不平衡的树上优于 DFS，因为它一旦到达第一个叶节点就终止。

*时间复杂度* : `O(n)`

*额外空间* : `O(1)`