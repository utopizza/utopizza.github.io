# 高斯混合模型的EM推导简单版


一、高斯混合模型

为简洁表达，以下把第 $k$ 个高斯模型 $\phi(y\mid\theta_k)$ 简记为 $\phi_k(y)$

那么高斯混合模型为：

$$
P(y\mid\theta)=\alpha_1\cdot\phi_1(y) + \alpha_2\cdot\phi_2(y) + \cdots + \alpha_K\cdot\phi_K(y)
$$

二、高斯混合模型参数估计的EM算法

输入：观测数据 ($y_1,y_2,\cdots,y_N$)，高斯混合模型
输出：高斯混合参数模型

(1) 取参数的初始值开始迭代

(2) E步：依据当前模型参数，计算每个分模型对每个观测数据的响应度

以第一个高斯模型 $\phi_1$ 为例：

对 $y_1$ 响应度：

$$
\hat{\gamma}_{11}=
\frac
{\alpha_1\cdot\phi_1(y_1)}
{\alpha_1\cdot\phi_1(y_1) + \alpha_2\cdot\phi_2(y_1) + \cdots + \alpha_K\cdot\phi_K(y_1)}
$$

对 $y_2$ 响应度：

$$
\hat{\gamma}_{12}=
\frac
{\alpha_1\cdot\phi(y_2\mid\theta_1)}
{\alpha_1\cdot\phi_1(y_2) + \alpha_2\cdot\phi_2(y_2) + \cdots + \alpha_K\cdot\phi_K(y_2)}
$$

$$\cdots$$

对 $y_N$ 响应度：

$$
\hat{\gamma}_{1N}=
\frac
{\alpha_1\cdot\phi(y_2\mid\theta_1)}
{\alpha_1\cdot\phi_1(y_N) + \alpha_2\cdot\phi_2(y_N) + \cdots + \alpha_K\cdot\phi_K(y_N)}
$$

(3) M步：计算新一轮迭代的模型参数

<div>
$$
\hat{\mu}_1=
\frac
{\hat{\gamma}_{11} \cdot y_1 + \hat{\gamma}_{12} \cdot y_2 + \cdots + \hat{\gamma}_{1N} \cdot y_N}
{\hat{\gamma}_{11} + \hat{\gamma}_{12} + \cdots + \hat{\gamma}_{1N}}
$$
</div>

<div>
$$
\hat{\sigma}_1^2=
\frac
{\hat{\gamma}_{11} \cdot (y_1-\mu_1)^2 + \hat{\gamma}_{12} \cdot (y_2-\mu_1)^2 + \cdots + \hat{\gamma}_{1N} \cdot (y_N-\mu_1)^2}
{\hat{\gamma}_{11} + \hat{\gamma}_{12} + \cdots + \hat{\gamma}_{1N}}
$$
</div>

<div>
$$
\hat{\alpha}_1=
\frac
{\hat{\gamma}_{11} + \hat{\gamma}_{12} + \cdots + \hat{\gamma}_{1N}}
{N}
$$
</div>

对每个高斯模型都如此求出每一轮迭代的新参数 $(\hat{\alpha}_k，\hat{\mu}_k，\hat{\sigma}_k^2)$

(4) 重复第(2)步和第(3)步，直到收敛。

如图：

![](2017-08-25-机器学习-高斯混合模型的EM推导简单版/1.jpg)

