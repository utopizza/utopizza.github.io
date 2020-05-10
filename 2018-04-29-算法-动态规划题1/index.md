# 动态规划题(1)


1、矩阵的最小路径和

给定一个矩阵 m ，从左上角开始，每次只能向右或者向下走，最后到达右下角的位置，定义路径上所有的数字之和为路径和。求所有路径中最小路径和。

思路：因为题目规定了每次只能向右走一步或者向下走一步，因此每一个位置的上一步只能是来自左边一个位置或者上边一个位置。那么走到某个位置的最小路径，只能是左边位置路径和与上边位置路径和中最小的那个，加上本位置的数字。

考虑边界条件：第一行的所有位置只能从左边走过来，第一列的所有位置只能从上面走下来。

用 $dp[i][j]$ 表示第 $i$ 行第 $j$ 列位置上的最小路径和，则

$$
dp[i][j]=min\{dp[i][j-1], dp[i-1][j]\}+m[i][j]
$$

```
private static int minPath(int[][] m) {
    if (m == null || m.length == 0 || m[0].length == 0) return 0;
    int row = m.length;
    int col = m[0].length;
    int[][] dp = new int[row][col];
    dp[0][0] = m[0][0];
    for (int i = 1; i < row; i++) dp[i][0] = dp[i - 1][0] + m[i][0];
    for (int j = 1; j < col; j++) dp[0][j] = dp[0][j - 1] + m[0][j];
    for (int i = 1; i < row; i++) {
        for (int j = 1; j < col; j++) {
            dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + m[i][j];
        }
    }
    return dp[row - 1][col - 1];
}
```

2、硬币找零——最少硬币数（硬币数量无限）

给定数组 coins，其元素均为正整数且不重复。每个元素代表一种面值的硬币。再给定一个换零的目标正整数 target，求用 coins 进行找零的最少硬币数。每种面值的硬币可以多次使用。

思路：因为题目规定每种面值的硬币可以无限重复使用，因此在找零的过程中，每一步都可以拿起 coins 数组中任意一个硬币，即每一步都有 coins.length 种选择。假设现在手上得到找零目标为 target 的最优找零方案。那么现在倒退一步来看，在拿起某个硬币 coins[j] 而得到 target 之前，手上的方案 target-coins[j] 必定也是最优的（可用算法导论的剪切-粘贴法证明）。如前面所述，每一步都有 coins.length 种选择，那么只需找出这些选择中最优的方案即可。即对每一个目标 i，只需搜索目标为 i-coins[j] 的所有方案并取最优的方案加 1 即可。

考虑边界条件：找零目标为 0 时的最少硬币数当然是 0。另外在遍历 coins 数组时，需要注意数组越界问题，即如果某个硬币的面值比要找零的目标还大的情况下，需要跳过这种情况。可通过先对 coins 数组进行排序来减少每一步的搜索空间。

设 dp[i] 表示找零目标为 i 的最少硬币数，则

$$
dp[i]=\min_{1 \leq j \leq coins.length} dp[i-coins[j]]+1 
$$

```
private static int minCoins(int[] coins, int target) {
    if (coins == null || coins.length == 0 || target <= 0) return 0;
    int[] dp = new int[target + 1];
    dp[0] = 0;
    int max = Integer.MAX_VALUE; 
    Arrays.sort(coins); // let "coins[j]<=i" to skip some useless solutions
    for (int i = 1; i <= target; i++) {
        dp[i] = max; 
        for (int j = 0; j < coins.length && coins[j] <= i; j++) {
            if (dp[i - coins[j]] != max) dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
        }
    }
    return dp[target] == max ? 0 : dp[target];
}
```

3、硬币找零——最少硬币数（硬币数量有限）

给定数组 coins，其元素均为正整数，有可能重复。每个元素代表一种面值的硬币。再给定一个换零的目标正整数 target，求用 coins 进行找零的最少硬币数。每个硬币只能使用一次。

思路：由于每个硬币只能用一次，所以不但需要记录每一步找零的目标，还需要记录用到了 coins 中哪些硬币，因此需要使用二位数组 dp[i][j]。dp[i][j] 表示使用 coins[0...i] 中的某些硬币来组成找零目标 j 的最少硬币数。主体思路和上一题差不多，只是这里不能“在每一步任意选取 coins.length 种硬币”了，而只能“要么用第 i 个硬币，要么不用”。假设现在手上已有使用前 i 个硬币得到找零目标为 j 的最优方案 dp[i][j]，那么它是如何得到的？同样倒推回去，它的前一步是：如果这一步使用了第 i 枚硬币 coins[i]，那么在使用这枚硬币前，手上的方案 dp[i-1][j-coins[i]] 必定也是一个最优的方案，此时使用这枚硬币后得到 dp[i][j]=dp[i-1][j-coins[i]]+1；如果这一步不使用 coins[i]，那么前一步手上的最优方案是 dp[i-1][j]，因为不使用这枚硬币，所以 dp[i][j]=dp[i-1][j]+0。最后 dp[i][j] 的最优解即为这两种情况的最小值。

边界条件：找零目标为 0 时最少硬币数为 0。搜索 coins 数组时同样要考虑硬币面值是否大于找零目标。如果小于找零目标，就按照上面的去两种方案中的最小值。如果大于找零目标，说明这个硬币永不上来找零，但是它的硬币数并不是0，而是直接等于不用这个硬币的方案，即 dp[i-1][j]。

$$
dp[i][j]=\min\{dp[i-1][j-coins[i]]+1, \quad dp[i-1][j]\}
$$

```
private static int minCoinsLimit(int[] coins, int target) {
        if (coins.length == 0 || target <= 0) return 0;
        int[][] dp = new int[coins.length][target + 1];
        int max = Integer.MAX_VALUE;

        for (int j = 1; j <= target; j++) dp[0][j] = max;
        if (target >= coins[0]) dp[0][coins[0]] = 1;

        for (int i = 1; i < dp.length; i++) {
            for (int j = 1; j <= target; j++) {
                if (j - coins[i] >= 0)
                    if (dp[i - 1][j - coins[i]] == max) dp[i][j] = dp[i - 1][j];
                    else dp[i][j] = Math.min(dp[i - 1][j - coins[i]] + 1, dp[i - 1][j]);
                else
                    dp[i][j] = max;
            }
        }
        return dp[coins.length - 1][target] == max ? 0 : dp[coins.length - 1][target];
}
```

4、硬币找零——找零方法数（硬币数量无限）

给定数组 coins， 其中所有的值均为正整数且不重复，每个值代表一种面值的硬币。现给定找零目标 target，规定每种硬币可以使用无数次，求一共有多少种找零方法。

思路：思路很简单，用 dp[i][j] 表示使用硬币 coins[0...i] 来组成找零目标 j 的方法数。同样倒回去想，dp[i][j]是怎么来的呢？显然是由 dp[i-1][x] 得来的。由于硬币数量无限，所以对第 i 种硬币，可以使用 0 到 K 个（$ K * coins[i] \leq j $）。因此 dp[i][j] 等于 dp[i-1][j-0*coins[i]]、 dp[i-1][j-1*coins[i]]、dp[i-1][j-2*coins[i]]、...、dp[i-1][j-K*coins[i]] 之和。

$$
dp[i][j]=\sum_{0\leq k \leq K} dp[i-1][j-k*coins[i]]
$$

```
private static int coins(int[] coins, int target) {
    if (coins == null || coins.length == 0 || target <= 0) return 0;
    int[][] dp = new int[coins.length][target + 1];
    for (int i = 0; i < coins.length; i++) dp[i][0] = 1;
    for (int j = 0; j * coins[0] <= target; j++) dp[0][j * coins[0]] = 1;
    for (int j = 1; j <= target; j++) {
        for (int i = 1; i < coins.length; i++) {
            int sum = 0;
            for (int k = 0; k * coins[i] <= j; k++) {
                sum += dp[i - 1][j - k * coins[i]];
            }
            dp[i][j] = sum;
        }
    }
    return dp[coins.length - 1][target];
}
```

这里还可以进一步优化，把最里面那层求累加和的 for 循环去掉：

<div>
$$
\begin{aligned}
dp[i][j]
& =\sum_{0\leq k \leq K} dp[i-1][j-k*coins[i]] \\
& =dp[i-1][j]+\sum_{1\leq k \leq K} dp[i-1][j-k*coins[i]] \\
& =dp[i-1][j]+dp[i][j-coins[i]]
\end{aligned}
$$
</div>

想一下为什么？

