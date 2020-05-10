# 最大子数组和



一、问题

给定一个整型数组，求其最大的子数组和。例如，给定数组：[-2,1,-3,4,-1,2,1,-5,4]，和最大的子数组为：[4,-1,2,1]，和为6。

二、递推式

设给定的数组为 $A$，用 $dp[i]$ 表示以 $A[i]$ 结尾的最大子数组之和。那么 $dp[i-1]$ 和 $dp[i]$ 有怎样的关系？由于给定的数组为整型，有正数也有负数，因此我们可以从子数组和的正负来考虑。显然，要使 $dp[i]$ 是最大的和，那么 以 $A[i-1]$ 结尾的子数组最大和 $dp[i-1]$ 如果是负数，则以 $A[i]$ 结尾的子数组的最大和 $dp[i]$ 应该去掉前面部分的负数，只取 $A[i]$  作为最大和，因为如果加上前面部分的和只会比 $A[i]$  更小；如果是正数，那么应该取 $A[i]$ 加上前面部分的 $dp[i-1]$ 作为最大和。形式化描述为：

$$
dp[i]=A[i]+(dp[i-1]>0?dp[i-1]:0)
$$

最终的最大子数组和就是数组 $dp[n]$ 的最大值。为了避免重复扫描该数组我们可以在计算该数组的时候维护一个当前最大值 $max$。

三、代码实现

```
public static int maxSubArray(int[] A) {
    int n = A.length;
    int[] dp = new int[n];//dp[i] means the maximum subarray ending with A[i];
    dp[0] = A[0];
    int max = dp[0];
    
    for(int i = 1; i < n; i++){
        dp[i] = A[i] + (dp[i - 1] > 0 ? dp[i - 1] : 0);
        max = Math.max(max, dp[i]);
    }
    
    return max;
}

public static void main(String[] args) {
    int[] A = {-2,1,-3,4,-1,2,1,-5,4};
    System.out.println(maxSubArray(A));
}
```

时间和空间复杂度都为 $O(n)$。

