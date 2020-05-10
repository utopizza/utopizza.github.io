# 动态规划题(2)


1、最长递增子序列（返回长度）

给定数组 arr，返回它的最长递增子序列的长度。

思路：显然对于数组的任何一个子序列，它必定以数组的某个元素结尾，因此可用 dp[i] 表示以 arr[i] 结尾的最长递增子序列的长度。那它是如何得来的呢？显然它就是在前面 i-1 个最长递增子序列后面追加或者不追加 arr[i] 而得到的 i-1 个新的递增子序列中，最长的那个。如果 arr[i] 大于 arr[j]，那么 arr[i] 就可以追加到以 arr[j] 结尾的最长递增子序列。

边界条件：如果 arr[i] 比它前面的 i-1 个元素都小，那么以 arr[i] 结尾的最长递增子序列就是它自己了，此时 dp[i]=1。

$$
dp[i]=\max\{ dp[j]+1 \} \text{,} \quad (0 \leq j < i \text{,} arr[j]<arr[i])
$$

```
private static int LIS(int[] arr) {
    if (arr == null || arr.length == 0) return 0;
    int[] dp = new int[arr.length];
    for (int i = 0; i < arr.length; i++) {
        dp[i] = 1;
        for (int j = 0; j < i; j++) {
            if (arr[i] > arr[j]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
    }
    int maxLen = 0;
    for (int i = 0; i < dp.length; i++) maxLen = Math.max(maxLen, dp[i]);
    return maxLen;
}
```

2、最长递增子序列（返回序列）

给定数组 arr，返回它的最长递增子序列。

思路：先按上面所述求出 dp 数组，然后再根据 dp 数组恢复出最长的子序列。恢复过程很简单：先找到 dp 中最大的元素的位置，假设是 i ，那么说明数组 arr 的最大递增序列以 arr[i] 结尾，也就是说它是所求序列的最后一个元素。然后从 arr[i] 开始往前扫描，如果遇到一个元素满足 dp[j]=d[i]-1 并且 arr[j] 小于 arr[i]，那么 arr[j] 便是倒数第二个元素。依次类推即可。

```
private static int[] LIS2(int[] arr, int[] dp) {
    int maxLen = 0;
    int lastIndex = 0;
    for (int i = 0; i < dp.length; i++) {
        if (dp[i] > maxLen) {
            maxLen = dp[i];
            lastIndex = i;
        }
    }

    int[] LIS = new int[maxLen];
    int LISIndex = maxLen - 1;
    LIS[LISIndex--] = arr[lastIndex];
    for (int j = lastIndex; j >= 0; j--) {
        if (arr[j] < arr[lastIndex] && dp[j] == dp[lastIndex] - 1) {
            LIS[LISIndex--] = arr[j];
            lastIndex = j;
        }
    }

    return LIS;
}
```

3、最长公共子序列（返回长度）

给定两个数组，返回两个数组的最长公共子序列的长度。

思路：用 dp[i][j] 表示 X[0...i] 与 Y[0...j] 的最长公共子序列长度。那么 dp[i][j] 是如何得到的？我们只需观察两个数组的最后一个元素 X[i] 和 Y[j]。如果 X[i] 和 Y[j] 相等，那么说明它就是 dp[i][j] 对应的公共子序列的最后一个元素。此时 dp[i][j] 由 dp[i-1][j-1]+1 得到。如果不相等，说明这个元素不是公共子序列的最后一个元素。那么此时 dp[i][j] 要么等于 dp[i-1][j]，要么等于 dp[i][j-1]。

边界：注意dp[0][0]，dp[i][0] 和 dp[0][j]。

<div>
$$
dp[i][j]=
\begin{cases}
dp[i-1][j-1]+1，& \text{if $x_{i}=y_{j}$} \\
max\{dp[i-1][j], \, dp[i][j-1]\}，& \text{if $x_{i} \neq y_{j}$}
\end{cases}
$$
</div>

```
private static int LCS(int[] X, int[] Y) {
    if (X == null || X.length == 0 || Y == null || Y.length == 0) return 0;
    int[][] dp = new int[X.length][Y.length];
    if (X[0] == Y[0]) dp[0][0] = 1;
    for (int i = 1; i < X.length; i++) dp[i][0] = Math.max(X[i] == Y[0] ? 1 : 0, dp[i - 1][0]);
    for (int j = 1; j < Y.length; j++) dp[0][j] = Math.max(X[0] == Y[j] ? 1 : 0, dp[0][j - 1]);
    for (int i = 1; i < X.length; i++) {
        for (int j = 1; j < Y.length; j++) {
            if (X[i] == Y[j]) dp[i][j] = dp[i - 1][j - 1] + 1;
            else dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
        }
    }
    return dp[X.length - 1][Y.length - 1];
}
```

4、最长公共子序列（返回序列）

给定两个数组，返回两个数组的最长公共子序列。

思路：想按上题求出 dp 数组，然后根据它来恢复序列。思路很简答，根据上面的思路倒推回去即可。

```
private static int[] LCS2(int[] X, int[] Y, int[][] dp) {
    int len = dp[X.length - 1][Y.length - 1];
    int[] lsc = new int[len];

    int m = X.length - 1;
    int n = Y.length - 1;
    int index = len - 1;
    while (index >= 0) {
        if (n > 0 && dp[m][n] == dp[m][n - 1]) n--;
        else if (m > 0 && dp[m][n] == dp[m - 1][n]) m--;
        else { // X[m]==Y[n]
            lsc[index--] = X[m];
            m--;
            n--;
        }
    }

    return lsc;
}
```

5、最长公共子数组/子串

给定两个数组，返回最长公共子数组。

思路：思路类似公共子序列，但是因为子数组/子串必须是元素连续的，因此略有不同。同样构造 dp 数组，dp[i][j] 表示以 X[i] 和 Y[j] 结尾的最长公共子数组/子串的长度。如果这两个元素不相等，那么 dp[i][j]=0，如果相等则 dp[i][j]=dp[i-1][j-1]+1。

边界：dp[0][0]，dp[i][0]，dp[0][j]。

<div>
$$
dp[i][j]=
\begin{cases}
dp[i-1][j-1]+1，& \text{if $x_{i}=y_{j}$} \\
0，& \text{if $x_{i} \neq y_{j}$}
\end{cases}
$$
</div>

```
private static int[][] LCSA(int[] X, int[] Y) {
    if (X == null || Y == null || X.length == 0 || Y.length == 0) return null;
    int[][] dp = new int[X.length][Y.length];
    if (X[0] == Y[0]) dp[0][0] = 1;
    for (int i = 0; i < X.length; i++) dp[i][0] = X[i] == Y[0] ? 1 : 0;
    for (int j = 0; j < Y.length; j++) dp[0][j] = X[0] == Y[j] ? 1 : 0;
    for (int i = 1; i < X.length; i++) {
        for (int j = 1; j < Y.length; j++) {
            if (X[i] == Y[j]) dp[i][j] = dp[i - 1][j - 1] + 1;
            else dp[i][j] = 0;
        }
    }
    return dp;
}
```

现在根据 dp 数组来恢复出公共子数组/子串。由于 dp[i][j] 表示的是以 X[i] 和 Y[j] 结尾的最长公共子数组/子串，只有当 X[i] 和 Y[j] 相等时才会增长 dp[i][j]。因此可以看到 dp 数组必定是沿着右下方增长的。只要遍历 dp 找到最大的元素 dp[m][n]，其值即为最长公共子数组/子串的长度，并且该子数组/子串以 X[m] 或者 Y[n] 结尾。直接从 X 或者 Y 里面截取即可。

```
private static int[] LCSA2(int[] X, int[] Y, int[][] dp) {
    int maxlen = 0;
    int lastIndex = 0;
    for (int i = 0; i < X.length; i++) {
        for (int j = 0; j < Y.length; j++) {
            if (dp[i][j] > maxlen) {
                lastIndex = i;
                maxlen = dp[i][j];
            }
        }
    }

    int[] lcsa = new int[maxlen];
    int index = lastIndex;
    for (int i = maxlen - 1; i >= 0; i--) {
        lcsa[i] = X[index--];
    }
    return lcsa;
}
```

