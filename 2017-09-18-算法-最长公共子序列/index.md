# 最长公共子序列



一、问题：给定两个序列 $X=< x_1, x_2, \cdots, x_m >$ 和 $Y=< y_1, y_2, \cdots, y_n >$，求它们的最长公共子序列（longest-common-subsequence，LCS）。

- 子序列定义：给定一个序列 $X=< x_1, x_2, \cdots , x_m >$，另一个序列 $Z=< z_1, z_2, \cdots, z_k >$ 满足如下条件时成为 $X$ 的子序列，即存在一个严格递增的 $X$ 的下标序列 $< i_1, i_2, \cdots, i_k >$，对所有的 $j=1, 2, \cdots, k$，满足 $x_{i_j}=z_j$。例如，$Z=< B,C,D,B >$ 是 $X=< A,B,C,B,D,A,B >$ 的子序列。
- 公共子序列定义：给定两个序列 $X$ 和 $Y$，如果 $Z$ 是 $X$ 的子序列，也是 $Y$ 的子序列，则称它是 $X$ 和 $Y$ 的公共子序列。例如，如果 $X=< A,B,C,B,D,A,B >$，$Y=< B,D,C,A,B,A >$，则 $< B,C,A >$ 是它们的一个公共子序列，但不是最长公共子序列，$< B,C,B,A >$ 和 $< B,D,A,B >$ 才是最长公共子序列。

二、划分子问题，导出递推式

LCS的最优子结构定理：给定两个序列 $X=< x_1, x_2, \cdots, x_m >$ 和 $Y=< y_1, y_2, \cdots, y_n >$，设 $Z=< z_1, z_2, \cdots, z_k >$ 为 $X$ 和 $Y$ 的任意 LCS。

- 1、如果 $x_m=y_n$，则 $z_k=x_m=y_n$，且 $Z_{k-1}$ 是 $X_{m-1}$ 和 $Y_{n-1}$ 的一个 LCS
- 2、如果 $x_m \neq y_n$，且 $z_k \neq x_m$ 意味着 $Z$ 是 $X_{m-1}$ 和 $Y$ 的一个 LCS
- 3、如果 $x_m \neq y_n$，且 $z_k \neq y_n$ 意味着 $Z$ 是 $X$ 和 $Y_{n-1}$ 的一个 LCS 

证明：

- 1、已知 $x_m=y_n$，假设 $z_k \neq x_m$ 或 $z_k \neq y_n$，那么可以将 $x_m$ 或 $y_n$ 追加到 $Z$ 中，得到一个长度为 $k+1$ 的 LCS，与 $Z$ 是 LCS 矛盾。因此必然有 $z_k=x_m=y_n$ 。这样，前缀 $Z_{k-1}$ 是 $X_{m-1}$ 和 $Y_{n-1}$ 的一个 LCS。同样用反证法证明：假设存在 $X_{m-1}$ 和 $Y_{n-1}$ 的一个长度大于 $k-1$ 的公共子序列 $W$，那么如果将 $x_m=y_n$ 追加到 $W$ 的末尾会得到 $X$ 和 $Y$ 的一个长度大于 $k$ 的公共子序列，矛盾。
- 2、已知 $z_k \neq x_m$，假设存在 $X_{m-1}$ 和 $Y$ 的一个长度大于 $k$ 的公共子序列 $W$，那么 $W$ 也是 $X_m$ 和 $Y$ 的一个长度大于 $k$ 的公共子序列，与 $Z$ 是 LCS 矛盾。
- 3、与情况2对称。

由上述定理可以推出：

- 1、如果 $x_m=y_n$，求 $X_{m-1}$ 和 $Y_{n-1}$ 的 LCS
- 2、如果 $x_m \neq y_n$，求以下两个子问题的 LCS，取较长的那个作为最优解：
 - 若 $z_k \neq x_m$，求 $X_{m-1}$ 和 $Y$ 的 LCS
 - 若 $z_k \neq y_n$，求 $X$ 和 $Y_{n-1}$ 的 LCS
 
定义 $c[i,j]$ 表示 $X_i$ 和 $Y_j$ 的LCS的长度，由上述得出递推式：

<div>
$$
c[i,j]=
\begin{cases}
0，& \text{if $i=0$，或 $j=0$} \\
c[i-1,j-1]+1，& \text{if $i,j>0$ 且 $x_{i}=y_{j}$} \\
max(c[i-1,j],c[i,j-1])，& \text{if $i,j>0$ 且 $x_{i} \neq y_{j}$}
\end{cases}
$$
</div>

三、实现

```
public static int LCSBottonUp(char[] X_0, char[] Y_0) {

    int x = X_0.length;
    char[] X = new char[x + 1];
    for (int i = 0; i < x; i++)
        X[i + 1] = X_0[i];

    int y = Y_0.length;
    char[] Y = new char[y + 1];
    for (int i = 0; i < y; i++)
        Y[i + 1] = Y_0[i];

    int[][] c = new int[x + 1][y + 1];

    for (int i = 0; i <= x; i++) {
        for (int j = 0; j <= y; j++) {
            if (i == 0 || j == 0) c[i][j] = 0;
            else {
                if (X[i] == Y[j]) c[i][j] = c[i - 1][j - 1] + 1;
                else c[i][j] = Math.max(c[i - 1][j], c[i][j - 1]);
            }
        }
    }

    return c[x][y];
}


public static void main(String[] args) {
    char[] X = {'A', 'B', 'C', 'B', 'D', 'B', 'A'};
    char[] Y = {'B', 'D', 'C', 'A', 'B', 'A'};
    System.out.println(LCSBottonUp(X, Y));
}
```

为了方便理解，通过预处理，使输入的序列下标从 $1$ 开始使用，即 $X[i]$ 表示 $x_i$，$Y[j]$ 表示 $y_j$，$c[i][j]$ 表示 $X_i$ 和 $Y_j$ 的公共子序列长度。由于外层循环的执行次数为输入序列 $X$ 的长度 $m$，内存循环的执行次数为输入序列 $Y$ 的长度 $n$，因此算法的时间复杂度为 $O(mn)$。另外使用了大小为 $m \cdot n$ 的二维数组，因此空间复杂度为 $O(mn)$。

