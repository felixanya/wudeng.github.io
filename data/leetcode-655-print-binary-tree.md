# Leetcode (655) Print Binary Tree

给定一个二叉树，输出一个双重数组，每一行代表二叉树的一层。空的节点用空字符串表示。

## 分析过程
先求出高度，分配好数组空间。然后用递归的方法遍历二叉树，将相应的值设置好。

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func printTree(root *TreeNode) [][]string {
    h := height(root)
    res := make([][]string, h)
    col := (1 << uint(h)) - 1
    for i := 0; i < h; i++ {
        res[i] = make([]string, col)
        for j := 0; j < col; j++ {
            res[i][j] = ""
        }
    }
    helper(root, res, 0, 0, col)
    return res
}

func helper(root *TreeNode, res [][]string, row, lo, hi int) {
    if root != nil {
        mid := (lo + hi) / 2
        res[row][mid] = strconv.Itoa(root.Val)
        helper(root.Left, res, row+1, lo, mid)
        helper(root.Right, res, row+1, mid+1, hi)
    }
}

func height(root *TreeNode) uint {
    if root == nil {
        return 0
    } else {
        h1 := height(root.Left)
        h2 := height(root.Right)
        if h1 >= h2 {
            return h1 + 1
        } else {
            return h2 + 1
        }
    }
}
```
