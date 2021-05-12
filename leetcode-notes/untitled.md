# 贪心算法

参考：[leetcode 101](https://github.com/changgyhub/leetcode_101/)

## 1 算法定义

参考[维基百科](https://en.wikipedia.org/wiki/Greedy_algorithm)，贪心算法是在每个阶段都遵循解决问题启发式局部最优解的算法 在许多问题中，贪婪策略通常不会产生最优解，但是，贪婪启发式方法可以产生局部最优解，这些局部最优解在合理的时间内近似于全局最优解。

例如，针对[旅行推销员问题](https://en.wikipedia.org/wiki/Travelling_salesman_problem)\(具有很高的计算复杂性\)的一个贪婪策略是以下启发式:“在旅程的每一步，访问最近的未访问的城市。”这种启发式方法并不打算找到最好的解决方案，但它以合理的步骤数终止;为如此复杂的问题找到最佳解决方案通常需要不合理的许多步骤。在数学优化中，贪心算法最优地解决具有拟阵性质的组合问题，并给出常因子近似的优化问题

## 2 例题

### 2.1 分类问题

### 2.1.1[455. 分发饼干](https://leetcode-cn.com/problems/assign-cookies/)

题目描述：

假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。对每个孩子 i，都有一个胃口值 g[i]，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 j，都有一个尺寸 s[j] 。如果 s[j] >= g[i]，我们可以将这个饼干 j 分配给孩子 i ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。


示例 1:

```
输入: g = [1,2,3], s = [1,1]
输出: 1
解释: 
你有三个孩子和两块小饼干，3个孩子的胃口值分别是：1,2,3。
虽然你有两块小饼干，由于他们的尺寸都是1，你只能让胃口值是1的孩子满足。
所以你应该输出1。
```

示例 2:

```
输入: g = [1,2], s = [1,2,3]
输出: 2
解释: 
你有两个孩子和三块小饼干，2个孩子的胃口值分别是1,2。
你拥有的饼干数量和尺寸都足以让所有孩子满足。
所以你应该输出2.
```


提示：

- 1 <= g.length <= 3 * 104
- 0 <= s.length <= 3 * 104
- 1 <= g[i], s[j] <= 231 - 1

解题思路：

给剩余孩子里最小饥饿度的孩子分配最小的能饱腹的饼干。把孩子和饼干分别排序。这样我们就可以从饥饿度最小的孩子和大小最小的饼干出发，计算有多少个对子可以满足条件。  

题解：

```javascript
/**
 * @param {number[]} g
 * @param {number[]} s
 * @return {number}
 */
var findContentChildren = function (g, s) {
    // 将g,s从小到大排序
    g.sort((a, b) => { return a - b });
    s.sort((a, b) => { return a - b });
    let sum = 0;
    // 这里最开始使用了双重循环可以更加优化
    // for (let i = 0; i < s.length; i++) {
    //     for (let j = 0; j < g.length; j++) {
    //         if (s[i] != -1 && g[j] != -1 && g[j] <= s[i]) {
    //             s[i] = -1;
    //             g[j] = -1;
    //             sum++;
    //         }
    //     }
    // }
    let cookies = kids = 0;
    while (cookies < s.length && kids < g.length) {
        if (s[cookies] >= g[kids]) {
            kids++;
            sum++;
        }
        cookies++;
    }
    return sum;
};
```

### 2.1.2 [455. 分发糖果](https://leetcode-cn.com/problems/candy/)

题目描述：

老师想给孩子们分发糖果，有 N 个孩子站成了一条直线，老师会根据每个孩子的表现，预先给他们评分。你需要按照以下要求，帮助老师给这些孩子分发糖果：

- 每个孩子至少分配到 1 个糖果。
- 评分更高的孩子必须比他两侧的邻位孩子获得更多的糖果。

那么这样下来，老师至少需要准备多少颗糖果呢？

示例 1：

```
输入：[1,0,2]
输出：5
解释：你可以分别给这三个孩子分发 2、1、2 颗糖果。
示例 2：
```

示例 2：

```
输入：[1,2,2]
输出：4
解释：你可以分别给这三个孩子分发 1、2、1 颗糖果。
     第三个孩子只得到 1 颗糖果，这已满足上述两个条件。
```

解题思路：

把所有孩子的糖果数初始化为 1；
先从左往右遍历一遍，如果右边孩子的评分比左边的高，则右边孩子的糖果数更新为左边孩子的糖果数加 1；再从右往左遍历一遍，如果左边孩子的评分比右边的高，且左边孩子当前的糖果数不大于右边孩子的糖果数，则左边孩子的糖果数更新为右边孩子的糖果数加 1。通过这两次遍历，分配的糖果就可以满足题目要求了。这里的贪心策略即为，在每次遍历中，只考虑并更新相邻一侧的大小关系  

题解：

```javascript
/**
 * @param {number[]} ratings
 * @return {number}
 */
var candy = function (ratings) {
    let arr = new Array(ratings.length).fill(1);
    let len = ratings.length;
    // 先从右到左
    for (let i = len - 1; i > 0; i--) {
        if (ratings[i - 1] > ratings[i]) {
            arr[i - 1] = arr[i] + 1;
        }
    }
    for (let i = 0; i < len - 1; i++) {
        if (ratings[i + 1] > ratings[i]) {
            arr[i + 1] = arr[i + 1] > arr[i] + 1 ? arr[i + 1] : arr[i] + 1;
        }
    }
    // 先从左到右
    // for (let i = 1; i < len; i++) {
    //     if (ratings[i] > ratings[i - 1]) {
    //         arr[i] = arr[i - 1] + 1;
    //     }
    // }
    // for (let i = len - 1; i > 0; i--) {
    //     if (ratings[i] < ratings[i - 1]) {
    //         arr[i - 1] = arr[i - 1] > arr[i] + 1 ? arr[i - 1] : arr[i] + 1;
    //     }
    // }
    // console.log(arr);
    return arr.reduce((a, b) => a + b);
};
```

## 2.2 区间问题

### 2.2.1 [435. 无重叠区间](https://leetcode-cn.com/problems/non-overlapping-intervals/)

题目描述：

给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。

注意:

1. 可以认为区间的终点总是大于它的起点。
2. 区间 [1,2] 和 [2,3] 的边界相互“接触”，但没有相互重叠。

示例 1:

```
输入: [ [1,2], [2,3], [3,4], [1,3] ]

输出: 1

解释: 移除 [1,3] 后，剩下的区间没有重叠。
```

示例 2:

```
输入: [ [1,2], [1,2], [1,2] ]

输出: 2

解释: 你需要移除两个 [1,2] 来使剩下的区间没有重叠。
```

示例 3:

```
输入: [ [1,2], [2,3] ]

输出: 0

解释: 你不需要移除任何区间，因为它们已经是无重叠的了。
```

解题思路：

求最少的移除区间个数，等价于尽量多保留不重叠的区间。在选择要保留区间时，区间的结尾十分重要：选择的区间结尾越小，余留给其它区间的空间就越大，就越能保留更多的区间。因此，我们采取的贪心策略为，优先保留结尾小且不相交的区间  

题解：

```javascript
/**
 * @param {number[][]} intervals
 * @return {number}
 */
var eraseOverlapIntervals = function (intervals) {
    sortarr = intervals.sort((a, b) => {
        return a[1] - b[1];
    })
    console.log(sortarr);
    let removed = 0, prev = sortarr[0][1];
    for (i = 1; i < sortarr.length; i++) {
        if (sortarr[i][0] < prev) {
            removed++;
        } else {
            prev = sortarr[i][1];
        }
    }
    return removed;
};
```

## 2.3 练习题

### 2.3.1 [605. 种花问题](https://leetcode-cn.com/problems/can-place-flowers/)

题目描述：

假设有一个很长的花坛，一部分地块种植了花，另一部分却没有。可是，花不能种植在相邻的地块上，它们会争夺水源，两者都会死去。

给你一个整数数组  flowerbed 表示花坛，由若干 0 和 1 组成，其中 0 表示没种植花，1 表示种植了花。另有一个数 n ，能否在不打破种植规则的情况下种入 n 朵花？能则返回 true ，不能则返回 false。

示例 1：

```
输入：flowerbed = [1,0,0,0,1], n = 1
输出：true
```

示例 2：

```
输入：flowerbed = [1,0,0,0,1], n = 2
输出：false
```


提示：

- 1 <= flowerbed.length <= 2 * 104
- flowerbed[i] 为 0 或 1
- flowerbed 中不存在相邻的两朵花
- 0 <= n <= flowerbed.length

解题思路：

题解：