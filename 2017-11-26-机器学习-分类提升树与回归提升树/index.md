# 分类提升树与回归提升树



一、提升树（boosting tree）

提升树是以**分类树**或**回归树**为**基本分类器**的提升方法。提升树被认为是统计学习中性能最好的方法之一。提升方法采用加法模型（即基函数的线性组合）与前向分步算法。对分类问题，决策树是二叉分类树；对回归问题，决策树是二叉回归树。

提升树模型可以表示为决策树的加法模型：

$$f_M(x)=\sum_{m=1}^{M}T(x;\theta_m)$$

其中，$T(x;\theta_m)$ 表示决策树，$\theta_m$ 为决策树的参数，$M$ 为决策树的个数。


二、二分类问题的提升树

对于二分类问题，提升树算法只需将 Adaboost 算法中的基本分类器限制为二分类树即可。

目前比较流行的方案是采用一种简单决策树作为基本分类器：单层决策树（decision stump，也称决策树桩）。它仅仅基于单个特征来执行决策，因此对应地只有一个树结点：根节点。它只能执行一次二分类，如 $x < v$ 或 $x > v$。

三、回归问题的提升树

回归树的形式化描述：设训练数据集 $T=\{(x_1,y_1),(x_2,y_2),\cdots,(x_N,y_N)\}$，$x_i \in X \subseteq R^n$，$y_i \in Y \subseteq R$，$X$ 是输入空间，$Y$ 是输出空间。如果将输入空间 $X$ 划分为 $J$ 个不相交的区域 $R_1,R_2,\cdots,R_J$，并且在每个区域上有确定的输出常量 $c_j$，那么回归树可以表示为：

$$T(x;\theta)=\sum_{j=1}^{J}c_j \cdot I(x \in R_j)$$

其中，参数 $\theta=\{(R_1,c_1),(R_2,c_2),\cdots,(R_J,c_J)\}$ 表示回归树的区域划分和各区域上的输出常量（即输出的回归值），$J$ 是回归树的复杂度（即**叶结点**个数）。

回归问题的提升树算法：

输入：如上，训练数据集 $T$，输入空间 $X$，输出空间 $Y$
输出：回归提升树 $f_M(x)$

1、初始化 $f_0(x)=0$

2、对 $M$ 个分类器，进行对应的第 $m=1,2,\cdots,M$ 轮学习。对第 $m$ 轮学习：

(1)、计算以前一轮为止，目前学习到的回归提升树 $f_{m-1}(x)$ 的残差

$$r_{mi}=y_i-f_{m-1}(x_i)，i=1,2,\cdots,N$$

(2)、拟合残差 $r_{mi}$ 学习得到第 $m$ 个回归树 $T(x;\theta_m)$

(3)、把该回归树加入 $f_{m-1}(x)$，得到新的回归提升树

$$f_m(x)=f_{m-1}(x)+T(x;\theta_m)$$

3、执行完 $M$ 轮学习后，得到最终的回归提升树

$$f_M(x)=\sum_{m=1}^{M}T(x;\theta_m)$$

