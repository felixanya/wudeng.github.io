# Leetcode (111) Mininum Depth of Binary Tree

求二叉树到叶子节点的最小高度。
二叉树的问题一般可以用递归解决。
注意二叉树如果只有一个分支，那么高度为那个仅有的分支。如果有两个分支，那么返回两个子树中较小的一个。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        } else if (root.left == null) {
            return 1 + minDepth(root.right);
        } else if (root.right == null) {
            return 1 + minDepth(root.left);
        } else {
            return 1 + Math.min(minDepth(root.left), minDepth(root.right));
        }
    }
}
```


```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func minDepth(root *TreeNode) int {
    if root == nil {
        return 0
    } else if root.Left == nil {
        return 1 + minDepth(root.Right)
    } else if root.Right == nil {
        return 1 + minDepth(root.Left)
    } else {
        left := minDepth(root.Left)
        right := minDepth(root.Right)
        if left <= right {
            return 1 + left
        } else {

            return 1 + right
        }
    }
}
```
