# 矩阵连乘



一、问题：给定 $n$ 个矩阵的链 $< A_1, A_2, \cdots , A_n >$，其中矩阵 $A_i$ $ (1 \leq i \leq n)$ 的规模为 $p_{i-1}  \cdot  p_i$，求完全括号化方案，使得计算乘积 $A_1A_2 \cdots\ A_n$ 所需标量乘法次数最少。

二、举例

以 $< A_1, A_2, A_3 >$ 相乘为例，假设三个矩阵的规模分别为 $10 \cdot 100，100 \cdot 5，5 \cdot 50$ 。

如果按 $ ((A_1  \cdot  A_2)  \cdot  A_3) $ 的顺序计算，则需要做 $ 10 \cdot 100 \cdot 5+10 \cdot 5 \cdot 50=5000+2500=7500 $ 次标量乘法运算。

如果按 $ (A_1  \cdot  (A_2  \cdot  A_3)) $ 的顺序计算，则需要做 $100 \cdot 5 \cdot 50+10 \cdot 100 \cdot 50=25000+50000=75000 $ 次标量乘法运算。

可以看到，第二种括号化方案导致的运算量是第一种情况的10倍。采取适当的括号化方案，能够极大的提高矩阵相乘运算的速度。

三、构造最优子结构，导出子问题递推式

对于 $n$ 个矩阵相乘进行括号化，可以先把前 $i$ $(1 \leq i \leq n-1)$ 个矩阵和后 $n-i$ 个矩阵加括号，也就是把这条矩阵链分成长度为 $i$ 和 $n-i$ 的两条子矩阵链，再继续对各子链递归地进行括号化。这样，矩阵括号化问题就类似于“钢条切割”问题了，只是这里求的是矩阵相乘的最小总标量乘法次数，而不是“钢条段的最大总价格”。

现在我们对某段矩阵链 $< A_i \cdots A_j >$ 进行一次括号化，我们选择在第 $k$ $(1 \leq k \leq j-1)$ 个矩阵处断开该链，使得该链分成两条子链 $< A_i \cdots A_k >$ 和 $< A_{k+1} \cdots A_j >$ 。

设 $r[i,j]$ 表示链 $< A_i \cdots A_j >$ 的总标量乘法次数，则由矩阵相乘的规则得

$$r[i,j]=r[i,k]+r[k+1,j]+p(i-1) \cdot p(k) \cdot p(j)$$

式中

- $r[i,k]$ 和 $r[k+1,j]$ 分别表示两条子链的各自的总标量乘法次数
- $ p(i-1) \cdot p(k) \cdot p(j) $ 表示两条子链合并时的标量乘法次数

对上面公式，同理于“钢条切割问题”，如果 $r[i,j]$ 是对链 $< A_i \cdots A_j >$ 括号化的所有方案中，总标量乘法次数最小（最优解），那么 $r[i,k]$ 和 $r[k+1,j]$ 一定是各自子链的最小总标量乘法次数（最优解）。

（因为当选定某个 $k$ 后，合并子链的代价随之确定，即公式中 $p(i-1) \cdot p(k) \cdot p(j)$ 部分。假设 $r[i,k]$ 和 $r[k+1,j]$ 不是两条子链各自的最优解，那么如果用它们各自的最优解（更小的 $r[i,k]$ 和 $r[k+1,j]$）代换进上面公式，那么必然产生比 $r[i,j]$ 更小的值，与 $r[i,j]$ 是最优解的假设矛盾。）

设 $m[i,j]$ 表示链 $< A_i \cdots A_j >$ 的最小总标量乘法次数（最优解），由上述思考过程可以得出最优解递推式：

<div>
$$
m[i,j]=
\begin{cases}
0, & \text{if $i=j$} \\
min_{i \leq k \leq j-1} (m[i,k]+m[k+1,j]+p(i-1) \cdot p(k) \cdot p(j)), & \text{if $i < j$}
\end{cases}
$$
</div>

四、动态规划法求解

```
public static int matrixChainBottonUp(int n, int[] s) {

    if (n < 2)
        return 0;
    int[] p = new int[s.length + 1];
    for (int i = 0; i < s.length; i++) {
        p[i + 1] = s[i];
    }

    int[][] m = new int[n + 1][n + 1];


    for (int length = 0; length <= n - 1; length++) {
        for (int i = 1; i + length <= n; i++) {
            int j = i + length;
            if (i == j) m[i][j] = 0;
            else {
                int min = Integer.MAX_VALUE;
                for (int k = i; k <= j - 1; k++) {
                    int temp = m[i][k] + m[k + 1][j] + p[2 * i - 1] * p[2 * k] * p[2 * j];
                    if (temp < min) min = temp;
                }
                m[i][j] = min;
            }
        }
    }

    return m[1][n];
}


public static void main(String[] args) {
    int n1 = 2;
    int[] p1 = {30, 35, 35, 15, 15, 5, 5, 10, 10, 20, 20, 25};
    int n2 = 3;
    int[] p2 = {10, 100, 100, 5, 5, 50};
    System.out.println(matrixChainBottonUp(n1, p1));
}
```

为了便于理解，通过预处理使所有数组的下标从 $1$ 开始使用，这样第 $i$ 个矩阵的行和列就分别是 $p[2 \cdot i-1]$ 和 $p[2 \cdot i]$，二维 $m$ 数组的元素 $m[i][j]$ 表示矩阵链 $< A_i \cdots A_j >$ 的最优解。

根据自底向上的思路，代码的计算过程为：

- m[1][1]，m[2][2]，m[3][3]，m[4][4]，m[5][5]，m[6][6]
- m[1][2]，m[2][3]，m[3][4]，m[4][5]，m[5][6]
- m[1][3]，m[2][4]，m[3][5]，m[4][6]
- m[1][4]，m[2][5]，m[3][6]
- m[1][5]，m[2][6]
- m[1][6]

其中，对 m[2][5]，算法会尝试在每一处$（2 \leq k < 5）$把链断开，然后求最优解：

<div>
$$
\begin{aligned}
m[2][5] & = min
\begin{cases}
m[2][2]+m[3][5]+p[2 \cdot 2-1] \cdot p[2 \cdot 2] \cdot p[5 \cdot 2]=13000 \\
m[2][3]+m[4][5]+p[2 \cdot 2-1] \cdot p[3 \cdot 2] \cdot p[5 \cdot 2]=7125 \\
m[2][4]+m[4][5]+p[2 \cdot 2-1] \cdot p[4 \cdot 2] \cdot p[5 \cdot 2]=11375
\end{cases} \\
& = 7125
\end{aligned}
$$
</div>

由于循环嵌套的深度为三层，每层的循环变量 $i,j,k$ 最多取 $n-1$ 个值，因此算法的运行时间为 $O(n^3)$。另外还需要大小为 $n*n$ 的二维数组来存放中间过程，因此算法的空间开销为 $O(n^2)$。

