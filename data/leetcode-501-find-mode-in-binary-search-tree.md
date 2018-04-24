# Leetcode (501) Find Mode in Binary Search Tree

找出二叉搜索树中出现频率最高的数字，如果有多个返回多个。


## 解题思路
遍历树，可以求出每个数字出现的次数。用map来统计每个数字出现的频率很合适。
但是得到这个map以后呢，怎么按照频率排序？

两次遍历即可：
可以先遍历map，找出最大的频率。然后再次遍历，将等于最大频率的加入数组即可。

个人感觉这个题目对于二叉搜索树的定义有点问题：
>
- The left subtree of a node contains only nodes with keys less than or equal to the node's key.
- The right subtree of a node contains only nodes with keys greater than or equal to the node's key.
- Both the left and right subtrees must also be binary search trees.

出现多个相等的时候，按照这个定义左孩子右孩子都可以包含父节点的数据？
这个貌似不对吧。

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func findMode(root *TreeNode) []int {
    m := make(map[int]int)
    inorder(root, &m)
    maxv := 0
    for _, v := range m {
        if v > maxv {
            maxv = v
        }
    }
    ans := make([]int, 0)
    for k, v := range m {
        if v == maxv {
            ans = append(ans, k)
        }
    }
    return ans
}

func inorder(root *TreeNode, m *map[int]int) {
    if root != nil {
        inorder(root.Right, m)
        (*m)[root.Val]++
        inorder(root.Left, m)
    }
}
```

可以将递归遍历改为莫里斯遍历：
```go
func findMode(root *TreeNode) []int {
    m := make(map[int]int)
    morrisInorder(root, &m)
    maxv := 0
    for _, v := range m {
        if v > maxv {
            maxv = v
        }
    }
    ans := make([]int, 0)
    for k, v := range m {
        if v == maxv {
            ans = append(ans, k)
        }
    }
    return ans
}

func morrisInorder(root *TreeNode, m *map[int]int) {
    for root != nil {
        if root.Left == nil {
            (*m)[root.Val]++
            root = root.Right
        } else {
            prev := root.Left
            for prev.Right != nil && prev.Right != root {
                prev = prev.Right
            }
            if prev.Right == nil {
                (*m)[root.Val]++
                prev.Right = root
                root = root.Left
            }
            if prev.Right == root {
                prev.Right = nil
                root = root.Right
            }
        }
    }
}
```

## O(1)空间方法

遍历两次，第一次找出最大的值，和最大值的个数。第二次收集最大值到数组。遍历因为要O(1)空间，可以用莫里斯遍历。
