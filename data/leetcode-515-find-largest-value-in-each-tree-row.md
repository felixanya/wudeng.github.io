# leetcode(515) Find Largest Value in Each Tree Row

找到二叉树中每一层最大的元素，返回数组。凡是二叉树的题目，一般都可以从递归的角度去思考。
如果递归也解决不了，那就dfs，bfs，总有一款适合你。这个题比较简单，就是遍历的时候记录一下节点
所在的高度，然后检查是否需要更新相应的最大值即可。为了初始化slice的大小，搞了一个求深度的函数，
同样也是递归。要注意的地方是数组的初始化，要用最小值而不是0。

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func largestValues(root *TreeNode) []int {
    h := height(root)
    //fmt.Println(h)
    ans := make([]int, h)
    for i:=0; i<h; i++ {
        ans[i] = math.MinInt32
    }
    helper(root, 0, ans)
    return ans
}

func helper(root *TreeNode, h int, res []int) {
    if root != nil {
        if root.Val > res[h] {
            res[h] = root.Val
        }
        helper(root.Left, h + 1, res)
        helper(root.Right, h + 1, res)
    }
}

func max(x, y int) int {
	if x < y {
		return y
	}
	return x
}

func height(root *TreeNode) int {
	if root == nil {
		return 0
	} else {
		return 1 + max(height(root.Left), height(root.Right))
	}
}
```

优化：其实不求深度也是可以的。赋值的时候比较一下深度和当前slice的len，如果相等用append，否则覆盖即可。
注意，如果要修改slice，传参数的时候要传指针。使用的时候要先dereference也就是前面加*。而且要注意优先级。
最好加括号。

```go
func largestValues(root *TreeNode) []int {
    ans := make([]int, 0)
    helper(root, 0, &ans)
    return ans
}

func helper(root *TreeNode, h int, res *[]int) {
    if root != nil {
        if h == len(*res) {
            *res = append(*res, root.Val)
        } else if root.Val > (*res)[h] {
            (*res)[h] = root.Val
        }
        helper(root.Left, h + 1, res)
        helper(root.Right, h + 1, res)
    }
}
```
