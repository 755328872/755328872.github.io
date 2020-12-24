---
title: 动态规划&分而治之思路讲解（以Leetcode 53题：求最大子序列为例）（转自评论区liweiwei1419大佬的讲解）
author: 花降
date: 2020-10-30 14:50:28
description: 动态规划笔记1
categories:
- 花降的编程笔记
tags:
- 算法
- Java
- 学习笔记
---
写在前面：本博客仅为本人总结记录笔记之用，不作任何商业用途，如有侵犯，请联系本人删除，谢谢。（转载链接见末尾）

## **方法一：暴力解法（了解，不感兴趣的朋友可以直接跳过）**

枚举所有的子区间：

使用双层循环，穷举所有的子区间；
然后再对子区间内的所有元素求和；
时间复杂度是立方级别的。
参考代码 1：

这里要注意一些边界条件：

变量 i 表示结尾的那个下标；
变量 j 表示从下标 0 依次向前走；

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < len; i++) {
            for (int j = 0; j <= i; j++) {
                int sum = sumOfSubArray(nums, j, i);
                res = Math.max(res, sum);
            }
        }
        return res;
    }

    private int sumOfSubArray(int[] nums, int left, int right) {
        // 子区间的和
        int res = 0;
        for (int i = left; i <= right; i++) {
            res += nums[i];
        }
        return res;
    }

}
```

复杂度分析：

时间复杂度：O(N^3)，这里 N 为数组的长度；
空间复杂度：O(1)。
提交以后发现「超时」，有两种情况：

程序当中写了「死循环」；
代码「正确」，复杂度较高，本解法属于这种情况。
优化：事实上，上面的代码有一些重复计算。这是因为相同前缀的区间求和，可以通过类似「状态转移」的方法得到。

例如：计算子区间 [1, 4] 的和可以在计算子区间 [1, 3] 的基础上，再加上 nums[4] 得到。

因此，只需要枚举子序的左端点，然后再扫描右端点，就可以减少一个级别的复杂度。

参考代码 2：

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < len; i++) {
            int sum = 0;
            for (int j = i; j < len; j++) {
                sum += nums[j];
                res = Math.max(res, sum);
            }
        }
        return res;
    }
}
```

复杂度分析：

时间复杂度：O(N^2)
空间复杂度：O(1)。
其实这道题是一个非常经典的动态规划问题。

该问题最早于 1977 年提出，但是直到 1984 年才被 Jay Kadane 发现了线性时间的最优解法。

## **方法二：动态规划（经典动态规划问题）**

**第 1 步：定义状态**
既然一个连续子数组一定要以一个数作为结尾，那么可以将状态定义成如下：

dp[i]：表示以 nums[i] 结尾的连续子数组的最大和。

类似的状态定义还有：「力扣」第 300 题：[「最长上升子序列」](https://leetcode-cn.com/problems/longest-increasing-subsequence/)。

以 「什么什么结尾」这种定义方式是动态规划的无后效性理论的应用，简而言之，确定一件事情，分类讨论才好开展，一个数在子序列中是否被选择，是这个问题的隐含条件。

**第 2 步：思考状态转移方程**
根据状态的定义，由于 `nums[i]` 一定会被选取，并且 `dp[i]` 所表示的连续子序列与 `dp[i - 1]` 所表示的连续子序列（有可能）就差一个 `nums[i]` 。

假设数组 nums 全是正数，那么一定有 `dp[i] = dp[i - 1] + nums[i]`。

在一般情况下 `dp[i - 1]` 有可能是负数，例如前几个数都是负数，突然来了一个正数。

于是分类讨论：

如果 `dp[i - 1] >= 0`，那么可以把 `nums[i]` 直接接在 `dp[i - 1]` 表示的那个数组的后面；
如果 `dp[i - 1] < 0`，那么加上前面的数反而越来越小了，于是「另起炉灶」，单独的一个 `nums[i]`，就是 `dp[i]`。
以上两种情况的最大值就是 `dp[i]` 的值，写出如下状态转移方程：

```java
dp[i] = dp[i - 1] + nums[i] ， if  dp[i−1]≥0
        = nums[i]  ------------if dp[i−1]<0
```


记为「状态转移方程 1」。

状态转移方程还可以这样写，反正求的是最大值，也不用分类讨论了，就这两种情况，取最大即可，因此还可以写出状态转移方程如下：

```java
dp[i] = max {nums[i], dp[i−1] + nums[i]}
```

记为「状态转移方程 2」。

动态规划的问题经常要分类讨论，这是因为动态规划的问题本来就有最优子结构的特征，即大问题的最优解通常由小问题的最优解得到，那么我们就需要通过分类讨论，得到大问题的小问题究竟是哪些。

**第 3 步：思考初始值**
`dp[0]` 根据定义，一定以 `nums[0]` 结尾，因此 `dp[0] = nums[0]`。

**第 4 步：思考输出**
这里状态的定义不是题目中的问题的定义，不能直接将最后一个状态返回回去。

输出应该是把所有的 `dp[0]、dp[1]、……、dp[n - 1]` 都看一遍，取最大值。 同样的情况也适用于「力扣」第 300 题：[「最长上升子序列」](https://leetcode-cn.com/problems/longest-increasing-subsequence/)。我经常在这一步「摔跟头」，请各位也留意。

参考代码 3：根据「状态转移方程 1」

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        int[] dp = new int[len];
        dp[0] = nums[0];

        for (int i = 1; i < len; i++) {
            if (dp[i - 1] >= 0) {
                dp[i] = dp[i - 1] + nums[i];
            } else {
                dp[i] = nums[i];
            }
        }

        // 最后不要忘记全部看一遍求最大值
        int res = dp[0];
        for (int i = 1; i < len; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }

}
```

参考代码 4：根据「状态转移方程 2」

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        int[] dp = new int[len];
        dp[0] = nums[0];

        for (int i = 1; i < len; i++) {
            dp[i] = Math.max(nums[i], dp[i - 1] + nums[i]);
        }

        int res = dp[0];
        for (int i = 1; i < len; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```
复杂度分析：
时间复杂度：O(N)
空间复杂度：O(N)
第 5 步：思考优化空间
既然当前状态只与上一个状态有关，我们可以将空间复杂度降到 O(1)。

参考代码 5：

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        // 起名叫 pre 表示的意思是「上一个状态」的值
        int pre = nums[0];
        int res = pre;
        for (int i = 1; i < len; i++) {
            pre = Math.max(nums[i], pre + nums[i]);
            res = Math.max(res, pre);
        }
        return res;
    }

}
```
复杂度分析：

时间复杂度：O(N)
空间复杂度：O(1)

## **方法三：分治法**

分治法的思路是这样的，其实也是分类讨论。

连续子序列的最大和主要由这三部分子区间里元素的最大和得到：

第 1 部分：子区间 [left, mid]；
第 2 部分：子区间 [mid + 1, right]；
第 3 部分：包含子区间 [mid , mid + 1] 的子区间，即 nums[mid] 与 nums[mid + 1] 一定会被选取。
对这三个部分求最大值即可。

说明：考虑第 3 部分跨越两个区间的连续子数组的时候，由于 nums[mid] 与 nums[mid + 1] 一定会被选取，可以从中间向两边扩散，扩散到底 选出最大值，具体请见「参考代码 6」。

参考代码 6：

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        return maxSubArraySum(nums, 0, len - 1);
    }

    private int maxCrossingSum(int[] nums, int left, int mid, int right) {
        // 一定会包含 nums[mid] 这个元素
        int sum = 0;
        int leftSum = Integer.MIN_VALUE;
        // 左半边包含 nums[mid] 元素，最多可以到什么地方
        // 走到最边界，看看最值是什么
        // 计算以 mid 结尾的最大的子数组的和
        for (int i = mid; i >= left; i--) {
            sum += nums[i];
            if (sum > leftSum) {
                leftSum = sum;
            }
        }
        sum = 0;
        int rightSum = Integer.MIN_VALUE;
        // 右半边不包含 nums[mid] 元素，最多可以到什么地方
        // 计算以 mid+1 开始的最大的子数组的和
        for (int i = mid + 1; i <= right; i++) {
            sum += nums[i];
            if (sum > rightSum) {
                rightSum = sum;
            }
        }
        return leftSum + rightSum;
    }

    private int maxSubArraySum(int[] nums, int left, int right) {
        if (left == right) {
            return nums[left];
        }
        int mid = (left + right) >>> 1;
        return max3(maxSubArraySum(nums, left, mid),
                maxSubArraySum(nums, mid + 1, right),
                maxCrossingSum(nums, left, mid, right));
    }

    private int max3(int num1, int num2, int num3) {
        return Math.max(num1, Math.max(num2, num3));
    }
}
```

复杂度分析：

时间复杂度：O(N*logN)，这里递归的深度是对数级别的，每一层需要遍历一遍数组（或者数组的一半、四分之一）；
空间复杂度：O(logN)，需要常数个变量用于选取最大值，需要使用的空间取决于递归栈的深度。

参考资料：
https://www.ge(去掉中文字和括号，防和谐)eksforgeeks.org/maximum-subarray-sum-using-divide-and-conquer-algorithm/

-------------------------------------------
作者：liweiwei1419
链接：https://leetcode-cn.com/problems/maximum-subarray/solution/dong-tai-gui-hua-fen-zhi-fa-python-dai-ma-java-dai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。