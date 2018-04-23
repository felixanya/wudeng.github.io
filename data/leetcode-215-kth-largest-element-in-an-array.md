# Leetcode (215) Kth Largest Element in an Array

## 方法一
最简单的办法就是先排序，直接用下标找到对应的元素，复杂对为O(n*logn)。


## 方法二
```cpp
int findKthLargest(vector<int>& nums, int k) {
    priority_queue<int, vector<int>, greater<int> > queue;
    int size = 0;
    for (vector<int>::iterator it=nums.begin(); it!=nums.end(); it++) {
        if (size < k) {
            queue.push(*it);
            size++;
        } else {
            if (*it >= queue.top()) {
                queue.push(*it);
                queue.pop();
            }
        }
    }
    return queue.top();
}
```

时间是10ms。
