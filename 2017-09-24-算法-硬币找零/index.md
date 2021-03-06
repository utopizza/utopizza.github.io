# 硬币找零



一、问题

假设有几种硬币，如1、3、5、10，并且数量无限。请找出能够组成某个数目的找零所使用最少的硬币数


形式化定义：设需要找零的总额为 $V$ ，一共有 $n$ 种硬币可以选择，其中第 $i$ 种硬币面值为 $c_i$，对应的使用数量为 $y_i$。则问题表述为：

$$min \quad \sum_{i=1}^{n}y_i , \quad y_i \in N \text{（自然数集）} $$

$$s.t. \quad \sum_{i=1}^{n} c_i \cdot y_i = V$$

二、划分子问题，导出递推式

今天查了一些资料，对动态规划有了新的理解。

[有博主说][1]：

> 动态规划的本质是对问题状态的定义，和对状态转移方程的定义。动态规划是通过拆分问题，定义问题状态和状态之间的关系，使得问题能够以递推（或者说分治）的方式去解决。如何拆分问题，才是动态规划的核心，而拆分问题，靠的就是状态的定义和状态转移方程的定义。

[也有博主说][2]：

> 计算机的本质是一个状态机，内存里存储的所有数据构成了当前的状态，CPU只能利用当前的状态计算出下一个状态（不要纠结硬盘之类的外部存储，就算考虑他们也只是扩大了状态的存储容量而已，并不能改变下一个状态只能从当前状态计算出来这一条铁律）当你企图使用计算机解决一个问题是，其实就是在思考如何将这个问题表达成状态（用哪些变量存储哪些数据）以及如何在状态中转移（怎样根据一些变量计算出另一些变量）。所以所谓的空间复杂度就是为了支持你的计算所必需存储的状态最多有多少，所谓时间复杂度就是从初始状态到达最终状态中间需要多少步！

回到我们的问题，要定义这个问题的状态和状态转移方程，首先需要分析这个问题的解的构建过程。那么找零的过程是怎么样的？很简单，每次可以拿起 $n$ 种硬币中，面值小于找零总额的任意一个。然后把找零总额减去拿起的硬币的面值，进入下一次选择。不断如此重复，直至拿起的硬币的总值等于找零的总额。（到这里，子问题的定义就显而易见了，这个问题的子问题就是每一次进行的硬币的选择，本次的选择只与之前的选择有关，因为每一次选择后剩余的找零总额会相应减小）

但是这样并不能保证硬币数是最小的。为了找到最优解，我们需要假设某个子问题已经得到了最优解，然后推出与它的子问题的最优解的关系，也就是最优解的递推关系式。设 $dp(i)$ 表示组合总价值为 $i$ 的零钱所需要的最小硬币数。因此每次可以选择 $n$ 种硬币中的一种，设本次选择的硬币为第 $j$ $(1 \leq j \leq n)$ 种，对应的面值为 $c[j]$，则在拿起该硬币之前的子问题即为：组合面值为 $dp(i-c[j])$ 的总额需要的最小硬币数。同样可以用“剪贴粘贴”的反正法证明其最优解为 $dp(i-c[j])-1$。形式化描述为：

<div>
$$
dp(i)=
\begin{cases}
0, & \text{if $i=0 $} \\
min_{1 \leq j \leq n}(dp(i-c[j])+1), & \text{if $i >0 \, , \, c[j] \leq i$} \\
\end{cases}
$$
</div>

三、算法实现

```
public static int coin(int[] c, int v) {
    int[] dp = new int[v + 1];
    for (int i = 0; i <= v; i++) {
        if (i == 0) dp[i] = 0;
        else {
            dp[i] = Integer.MAX_VALUE;
            for (int j = 1; c[j] <= i && j < c.length; j++) {
                int temp = dp[i - c[j]] + 1;
                if (temp < dp[i]) dp[i] = temp;
            }
        }
    }
    return dp[v];
}

public static void main(String[] args) {
    //从下标1开始表示第1种硬币
    int[] c = {0, 1, 3, 5, 10};
    System.out.println(coin(c, 3));
}
```

外层的 $for$ 循环取值范围为 $0$ 到 $V$，内层的 $for$ 循环的取值范围为 $0$ 到 $n$，因此时间复杂度为 $O(V \cdot n)$    。空间复杂度为 $O(V)$。



  [1]: https://www.zhihu.com/question/23995189/answer/35324479
  [2]: https://www.zhihu.com/question/23995189/answer/35429905

