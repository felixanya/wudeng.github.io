# 寻路


## 地图的表示
* Grid Search Space
* Way Point Graph
* Navigation Mesh

## 对角移动
* Always 总是允许
* Never 总是禁止
* IfAtMostOneObstacle 最多一个障碍物则允许
* OnlyWhenNoObstacles 仅当没有障碍

这四种策略决定了一个节点的子节点个数，以及启发函数的选择。
```js
/**
 * Get the neighbors of the given node.
 *
 *     offsets      diagonalOffsets:
 *  +---+---+---+    +---+---+---+
 *  |   | 0 |   |    | 0 |   | 1 |
 *  +---+---+---+    +---+---+---+
 *  | 3 |   | 1 |    |   |   |   |
 *  +---+---+---+    +---+---+---+
 *  |   | 2 |   |    | 3 |   | 2 |
 *  +---+---+---+    +---+---+---+
 *
 *  When allowDiagonal is true, if offsets[i] is valid, then
 *  diagonalOffsets[i] and
 *  diagonalOffsets[(i + 1) % 4] is valid.
 * @param {Node} node
 * @param {DiagonalMovement} diagonalMovement
 */
Grid.prototype.getNeighbors = function(node, diagonalMovement) {
    var x = node.x,
        y = node.y,
        neighbors = [],
        s0 = false, d0 = false,
        s1 = false, d1 = false,
        s2 = false, d2 = false,
        s3 = false, d3 = false,
        nodes = this.nodes;

    // ↑
    if (this.isWalkableAt(x, y - 1)) {
        neighbors.push(nodes[y - 1][x]);
        s0 = true;
    }
    // →
    if (this.isWalkableAt(x + 1, y)) {
        neighbors.push(nodes[y][x + 1]);
        s1 = true;
    }
    // ↓
    if (this.isWalkableAt(x, y + 1)) {
        neighbors.push(nodes[y + 1][x]);
        s2 = true;
    }
    // ←
    if (this.isWalkableAt(x - 1, y)) {
        neighbors.push(nodes[y][x - 1]);
        s3 = true;
    }

    if (diagonalMovement === DiagonalMovement.Never) {
        return neighbors;
    }

    if (diagonalMovement === DiagonalMovement.OnlyWhenNoObstacles) {
        d0 = s3 && s0;
        d1 = s0 && s1;
        d2 = s1 && s2;
        d3 = s2 && s3;
    } else if (diagonalMovement === DiagonalMovement.IfAtMostOneObstacle) {
        d0 = s3 || s0;
        d1 = s0 || s1;
        d2 = s1 || s2;
        d3 = s2 || s3;
    } else if (diagonalMovement === DiagonalMovement.Always) {
        d0 = true;
        d1 = true;
        d2 = true;
        d3 = true;
    } else {
        throw new Error('Incorrect value of diagonalMovement');
    }

    // ↖
    if (d0 && this.isWalkableAt(x - 1, y - 1)) {
        neighbors.push(nodes[y - 1][x - 1]);
    }
    // ↗
    if (d1 && this.isWalkableAt(x + 1, y - 1)) {
        neighbors.push(nodes[y - 1][x + 1]);
    }
    // ↘
    if (d2 && this.isWalkableAt(x + 1, y + 1)) {
        neighbors.push(nodes[y + 1][x + 1]);
    }
    // ↙
    if (d3 && this.isWalkableAt(x - 1, y + 1)) {
        neighbors.push(nodes[y + 1][x - 1]);
    }

    return neighbors;
};
```

## 启发函数
* manhattan(曼哈顿)
    * 不允许对角线移动
* euclidean(欧几里德)
* octile
    * 计算简单
* chebyshev(切比雪夫)

```js
manhattan: function(dx, dy) {
  return dx + dy;
},

euclidean: function(dx, dy) {
  return Math.sqrt(dx * dx + dy * dy);
},

// min(dx, dy)*SQRT2 + abs(dx - dy)
// min(dx, dy)*(SQRT2-1) + max(dx, dy)
octile: function(dx, dy) {
  var F = Math.SQRT2 - 1;
  return (dx < dy) ? F * dx + dy : F * dy + dx;
},

chebyshev: function(dx, dy) {
  return Math.max(dx, dy);
}
```

当不允许对角移动的时候，默认选择曼哈顿，当允许对角移动时默认选择Octile.
```js
// When diagonal movement is allowed the manhattan heuristic is not
//admissible. It should be octile instead
if (this.diagonalMovement === DiagonalMovement.Never) {
    this.heuristic = opt.heuristic || Heuristic.manhattan;
} else {
    this.heuristic = opt.heuristic || Heuristic.octile;
}
```

## A*

维护一个根据f排序的OpenList(PriorityQueue, MinHeap，桶排序)，初始将起点加入OpenList。每次从OpenList中取出一个节点Node，
如果Node等于终点，则回溯起点构造路径并返回，否则设置Node为Closed，得到Node的相邻节点，遍历相邻节点，如果节点已经Open，重新计算f，
如果得到更小的g，则更新OpenList中的节点信息，如果已经Close，则跳过。如果没有Open也没有Close则计算新的f值，加入OpenList。
直到OpenList为空，说明终点不可达。上下左右相邻格子距离为1，对角格子距离为SQRT2。

## Best First Search

跟A*的区别在于对评估函数的定义。A*算是一种BFS的一种特殊情况。如果不考虑g，只考虑h评估函数，则成为贪心BFS或者纯启发搜索。

```js
function BestFirstFinder(opt) {
    AStarFinder.call(this, opt);

    var orig = this.heuristic;
    this.heuristic = function(dx, dy) {
        return orig(dx, dy) * 1000000; // 放大h的权重
    };
}
```


## 双向A*搜索

同时从起点和终点出发，相遇的时候构造路径，不能保证是最短路径，但能保证找到最短路径。

* opened :: BY_START | BY_END
* closed :: true | undefined
* g
* h
* f = g + h
* parent


## Breadth First Search

宽度有限搜索，效率不高。搜索空间太大，双向会好一点，实用价值不大。

## Dijstra Search

算是A*的特殊形式。不考虑启发函数，只考虑g。效率太差，基本相当于暴力搜索，实用价值不大。
跟宽度优先搜索相比，多了一个对角距离的的考虑。


## JPS

> avoid redundant paths on grids

2011年由Daniel Harabor和Alban Grastien两人提出，相比A*，JPS能够将性能提升10倍左右。
2014年，Daniel Harabor和Alban Grastien又提出了JPS+，又将JPS性能提升了10倍左右。
巧合的是GAME AI PRO(1,2,3)的作者Steve Rabin也独立提出了同样的算法。Steve在15thGDC上提出了JPS+ GB算法，
能够将A*的性能提升1000倍以上。

JPS的核心思想是减少网格地图的冗余等效路径。我们不需要找到所有的最佳路径，只需要一条就够了。
加入开放列表的节点为跳点。



## JPS+

JPS 加上预处理。提前计算每个节点八个方向上的跳点。


straight jump points
diagonal jump points

wall distances

fast stack and unsorted open list


## Goal Bouding

> avoid wrong directions on any kind of map

避免错误的方向。O(n2)的预处理时间。

## JPS+ Goal Bouding

目前应该是最快的寻路算法。


## 寻路架构优化

同样是A*算法，最好的实现与最差的实现速度相差了百倍。所以，算法的优化也相当关键。
- 高质量的启发函数
    - 提前计算好路径保存起来。（Floyd-Warshall）
    - 无损压缩
    - 有损压缩 Euclidean embedding
- 搜索空间
    - 格子，路点，导航网格
- 提前分配好需要的内存
- Overestimating the Heuristic
    - 如果要最优路径，那么启发函数不能大于实际距离
    - 然而使用大一点的启发函数，可以更快速的得到一个次优路径
    - f(x) = g(x) + (h(x) * weight)
        - weight = 0, Dijstra, 保证最优路径，但是效率堪忧
        - weight = 1, 经典A*
        - weight > 1, Gready Best-First search，非最优，侧重于尽快的找到目标
- 更好的启发函数
    - Euclidean
    - octile: max(dx, dy) + 0.41 * min(dx, dy), 对角距离为1.41 = sqrt(2)
    - Manhattan
- Open List Sorting
    - PriorityQueue, Heap
    - weak heap
    - f-cost lists fifo
    - array for short list, 尾部加入，移除时跟尾部交换
- 搜索过程中不回溯 visited JPS

## 参考文献

* http://theory.stanford.edu/~amitp/GameProgramming/index.html
* https://zerowidth.com/2013/05/05/jump-point-search-explained.html
* http://www.gdcvault.com/play/1022094/JPS-Over-100x-Faster-than
* https://github.com/SteveRabin/JPSPlusWithGoalBounding
* http://qiao.github.io/PathFinding.js/visual/
* http://web.cs.du.edu/~sturtevant/ai-s11/Lecture01.pdf
* http://web.cs.du.edu/~sturtevant/ai-s11/Lecture05.pdf
* https://en.wikipedia.org/wiki/Best-first_search
* http://users.cecs.anu.edu.au/~dharabor/data/papers/harabor-grastien-aaai11.pdf
* http://www.policyalmanac.org/games/aStarTutorial.htm
* [Improving Jump Point Search](http://users.cecs.anu.edu.au/~dharabor/data/papers/harabor-grastien-icaps14.pdf)
* http://www.gad.qq.com/article/detail/10044
* GameAI Pro by SteveRabin & Nathan R. Sturtevant
