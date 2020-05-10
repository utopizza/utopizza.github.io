# 最长递增子序列



一、问题

给定一个长度为 $N$ 的数组，找出一个最长的单调自增子序列的长度。例如：给定一个长度为 6 的数组 $A=< 5，6，7，1，2，8 >$，则其最长的单调递增子序列为 $< 5，6，7，8 >$，长度为4。

二、寻找最优子结构，导出递推式

设前 $i$ 个元素的最长递增子序列长度（即第 $i$ 个子问题最优解）表示为 $dp(i)$。那么第 $i$ 个子问题的最优解 $dp(i)$ 与前面 $i-1$ 个子问题各自的最优解 $dp(k)$ $(1 \leq k \leq i-1)$ 有着怎么样的递推关系？

考察前 $i-1$ 个子问题中第 $k$ $(1 \leq k \leq i-1)$ 个子问题对应的最长递增子序列。如果原序列第 $i$ 个元素 $A[i]$ 大于等于该序列的最后一个元素（设为 $last[k]$），则 $A[i]$ 可以追加到此递增子序列的末尾。此时有 $dp(i)=dp(k)+1$；反之如果 $A[i]$ 小于该序列的最后一个元素 $last[k]$，则 $A[i]$ 不能追加到此递增子序列的末尾，此时 $dp(i)=dp(k)$。由于 $1 \leq k \leq i-1$，因此我们只要遍历前 $i-1$ 个元素对应的 $i-1$ 个最长子序列，并考察第 $i$ 个元素追加（或者不追加）到这些子序列末尾后得到的 $i-1$ 个 $dp(i)$，其中最大一个的即为第 $i$ 个问题的最优解。形式化描述为：

<div>
$$
dp(i)=
\begin{cases}
1, & \text{if $i=1$} \\
max_{1 \leq k \leq i-1} \{ dp(k) \} , & \text{if $i>1,A[i] < last[k]$} \\
max_{1 \leq k \leq i-1} \{ dp(k)+1 \}, & \text{if $i>1,A[i] \geq last[k]$} \\
\end{cases}
$$
</div>

三、代码实现

```
public static int longestIncreasingSubsequence(int[] A) {
    int n = A.length - 1;
    int[] dp = new int[n + 1];
    int[] last = new int[n + 1];
    for (int i = 1; i <= n; i++) {
        if (i == 1) {
            dp[i] = 1;
            last[i] = A[1];
        } else {
            dp[i] = Integer.MIN_VALUE;
            for (int k = 1; k <= i - 1; k++) {
                if (A[i] >= last[k] && dp[k] + 1 > dp[i]) {
                    dp[i] = dp[k] + 1;
                    last[i] = A[i];
                } else if (dp[k] > dp[i]) {
                    dp[i] = dp[k];
                    last[i] = last[k];
                }
            }
        }
    }
    return dp[n];
}

public static void main(String[] args) {
    // 第一个元素从下标1开始
    int[] A = {0, 9, 6, 7, 1, 9, 10};
    System.out.println(longestIncrementSubsequence(A));
}
```

使用 $last[k]$ 来记录前 $k$ 个元素的最长递增子序列的最后一个元素。算法使用了两层 $for$ 循环，外层取值范围 $1$ 到 $n$，内层 $1$ 到 $i-1$，因此时间复杂度为 $O(n^2)$。另外由于使用了长度为 $n$ 的一维数组，因此空间复杂度为 $O(n)$。

