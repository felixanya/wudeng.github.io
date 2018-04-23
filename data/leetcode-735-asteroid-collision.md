# Leetcode (735) Asteriod Collistion

给定一个数组代表小行星。整数代表向右，负数代表向左。绝对值代表大小。所有行星移动速度大小是一样的。
如果发生碰撞，小的爆炸，如果大小一样，两个都爆炸。求最后稳定的数组。

分析过程：
维护一个稳定的行星数组stack。从左往右遍历给定数组，每次加入一颗行星，看它会不会跟之前的行星发生碰撞。
如果没有碰撞，直接加入，如果碰撞，处理碰撞。
* 如果stack为空，直接加入
* 如果stack非空
    - 如果stack最右行星向左移动，那么无论新加入的行星怎么移动，都不会碰撞，直接加入stack
    - 如果stack最右行星向右移动，新加入的行星也向右移动，也不会碰撞，直接加入stack
    - 如果stack最右行星向右移动，新加入行星向左移动，这时候就会发生碰撞了
        - 如果结果为正，最右行星胜出，新行星爆炸
        - 如果结果为负，最右行星爆炸，次右行星变成最右行星，新行星继续跟新的最右行星比较
        - 如果结果为0，两个都爆炸

其实这个稳定行星数组stack扮演了一个栈的角色。提供压栈、弹栈等操作。

```go
func asteroidCollision(asteroids []int) []int {
    size := len(asteroids)
    stack := make([]int, 0)
    stackLen := 0
    for i := 0; i < size; i++ {
        if stackLen == 0 || stack[stackLen-1] < 0 || asteroids[i] > 0 {
            stack = append(stack, asteroids[i])
            stackLen++
        } else  {
            collistion := asteroids[i] + stack[stackLen-1]
            if collistion < 0 {
                stackLen--
                stack = stack[:stackLen]
                i--
            } else if collistion == 0 {
                stackLen--
                stack = stack[:stackLen]
            }
        }
    }
    return stack
}
```
