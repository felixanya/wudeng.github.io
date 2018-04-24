# Leetcode (215) Kth Largest Element in an Array

从一个未排序的数组中找到第K大的元素。

## 方法一
最简单的办法就是先排序，直接用下标找到对应的元素，复杂对为O(n*logn)。

## 方法二
使用小根堆，维持一个大小为k的堆，遍历数组，如果元素大于根元素，就替换根元素。
c++中可以用优先级队列来代替。复杂度为O(nlogk)。

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

## 递归


类似快速排序。找到一个pivot元素，将数组分为两个部分，左边大于pivot，右边小于pivot。中间是pivot。
至于等于pivot的元素放左边还是右边，需要仔细考虑一下。

partition的过程，使用两个指针，一个i，一个j，i之前的元素都大于pivot，i和j之间的大于pivot。j之后的还没有检测。
当j所在的元素大于pivot的时候，需要放到位置i那里去。假设我们把等于pivot的放到前面，意味着我们需要先找到一个位置i，
使得i前面的元素都是小于等于pivot的。而如果把等于pivot的放后面，我们找都不用找，直接用起始位置即可。

j遍历完数组以后，我们需要把pivot放在中间也就是i的位置，意味着我们需要维护i作为一个空位。也就是说i之前的都是大于pivot，
i之后的都是小于等于pivot的。遍历结束我们让i位置等于pivot。这样就完成了partition。

复杂度：最优O(N)，最差O(N^2)。

```go
// [s, e) 保持左闭右开
func helper(nums []int, s, e, kth int) int {
    pivot := nums[s]
    i := s
    for j := i + 1; j < e; j++ {
        if nums[j] > pivot {
            nums[i] = nums[j]
            i++
            nums[j] = nums[i]
        }
    }
    nums[i] = pivot
    // 到这里，nums[i] = pivot, i前面的都是大于i的，后面都是小于等于pivot的

    if i - s == kth - 1 {
        return pivot
    } else if (i - s > kth - 1) {
        return helper(nums, s, i, kth)
    } else {
        return helper(nums, i + 1, e, kth - 1 - (i - s))
    }
}

func findKthLargest(nums []int, k int) int {
    return helper(nums, 0, len(nums), k)
}
```

最后结果：36毫秒


另外一个partition的方法：也是i，j两个指针。i往前，j往后。注意不要越界。
i左边：大于等于pivot，
j右边，小于pivot

```go
// [s, e) 保持左闭右开
func helper(nums []int, s, e, kth int) int {
    pivot := nums[s]
    i, j := s + 1, e - 1
    for i <= j {
        for j > s && nums[j] < pivot {
            j--
        }
        for i < e && nums[i] >= pivot {
            i++
        }
        if i < j {
            nums[i], nums[j] = nums[j], nums[i]
        }
    }
    i--
    nums[s], nums[i] = nums[i], nums[s]

    // 到这里，nums[i] = pivot, i前面的都是大于等于pivot的，后面都是小于pivot的

    if i - s == kth - 1 {
        return pivot
    } else if (i - s > kth - 1) {
        return helper(nums, s, i, kth)
    } else {
        return helper(nums, i + 1, e, kth - 1 - (i - s))
    }
}

func findKthLargest(nums []int, k int) int {
    return helper(nums, 0, len(nums), k)
}
```

16ms
