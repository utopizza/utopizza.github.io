# 高斯混合模型的EM推导



一、高斯混合模型：

$$P(y \mid \theta) = \sum_{k=1}^{K} \alpha_k \cdot \phi(y \mid \theta_k)$$ 

其中，$\alpha_k \text{是系数，} \alpha_k \geq0 \text{，且 } 
\sum_{k=1}^{K} \alpha_k=1 \text{；}$

$\phi(y \mid \theta_k) = \frac{1}{\sqrt{2\pi}} exp (-\frac{(y-\mu_k)^2}{2\sigma_k^2}) \text{，}\theta_k = (\mu_k ,\sigma_k^2)\text{ 是第k个高斯模型。}$

二、高斯混合模型参数估计的EM算法

输入：观测数据 ($y_1,y_2,\cdots,y_N$)，高斯混合模型
输出：高斯混合参数模型

(1) 取参数的初始值开始迭代

(2) E步：依据当前模型参数，计算分模型 k 对观测数据 $y_j$ 的响应度

<div>
$$\hat{\gamma}_{jk} = \frac{\alpha_k \cdot \phi(y_j \mid \theta_k)}{\sum_{k=1}^{K} \alpha_k \cdot \phi(y_j \mid\theta_k)}\text{，}j=1,2,\cdots,N;k=1,2,\cdots,K$$
</div>

(3) M步：计算新一轮迭代的模型参数

<div>
$$\hat{\mu}_k =\frac{\sum_{j=1}^{N} \hat{\gamma}_{jk} \cdot y_j}{\sum_{j=1}^{N} \hat{\gamma}_{jk}}\text{，}k=1,2,\cdots,K$$
</div>

<div>
$$\hat{\sigma}_k^2 = \frac{\sum_{j=1}^{N} \hat{\gamma}_{jk}\cdot (y_j - \mu_k)^2}{\sum_{j=1}^{N} \hat{\gamma}_{jk}}\text{，}k=1,2,\cdots,K$$
</div>

<div>
$$\hat{\alpha}_k =\frac{\sum_{j=1}^{N} \hat{\gamma}_{jk}}{N}\text{，}k=1,2,\cdots,K$$
</div>

(4) 重复第(2)步和第(3)步，直到收敛。

三、上述算法基于似然估计进行推导：

(1). 似然函数：

<div>
$$
\begin{aligned}
P(y,\gamma \mid \theta) &=
\prod_{j=1}^{N} P(y_j, (\gamma_{j1},\cdots,\gamma_{jK}) \mid \theta) \\
&=\prod_{k=1}^{K} \prod_{j=1}^{N} [\alpha_k \cdot \phi(y_j \mid \theta_k)]^{\gamma_{jk}} \\
&=\prod_{k=1}^{K} \alpha_k \prod_{j=1}^{N} [\phi(y_j \mid \theta_k)]^{\gamma_{jk}} \\
&=\prod_{k=1}^{K} \alpha_k \prod_{j=1}^{N} 
[\frac{1}{\sqrt{2\pi}} exp(- \frac{(y-\mu_k)^2}{2\sigma_k^2})]^{\gamma_{jk}} \\
\end{aligned}
$$
</div>

式中，$n_k=\sum_{j=1}^{N} \gamma_{jk}$，$\sum_{k=1}^{K} n_k=N$。

(2) 在 $E$ 步确定 $Q$ 函数：

<div>
$$
\begin{aligned}
Q(\theta,\theta^{(i)}) &= E[logP(y,\gamma \mid \theta) \mid y, \theta^{(i)}] \\
&= \cdots \\
&= \sum_{k=1}^{K} \{ n_k \cdot log\alpha_k + \sum_{j=1}^{N} [log(\frac{1}{\sqrt{2\pi}}) - log(\sigma_k)- \frac{1}{2\sigma_k^2}(y_j-\mu_k)^2] \}
\end{aligned}
$$
</div>

(3) 在 $M$ 步中，求 $Q(\theta,\theta^{(i)})$ 对 $\theta$ 的极大值，即求新一轮迭代的模型参数：

<div>
$$
\theta^{(i)} = 
\underset{\theta}{\operatorname{argmax}} Q(\theta,\theta^{(i)})
$$
</div>

求 $\hat{\mu}_k，\hat{\sigma}_k^2$ 只需将上式分别对 $\hat{\mu}_k，\hat{\sigma}_k^2$ 求导并令导数等于0即可，这是常规的求极值的方法。但是对于 $\hat{\alpha}_k$，因为存在着 $\sum_{k=1}^{K} \alpha_k = 1$ 这个条件，所以这里要注意对有多个有关系的变量的求偏导问题。

(4) 对 $\alpha_k$ 求偏导：

<div>
$$
\frac{\partial Q}{\partial \alpha_k} = 
\frac{\partial\{\sum_{k=1}^{K} [ n_k \cdot log(\alpha_k) ]\} }
{\partial \alpha_k} \\
s.t. \quad \sum_{k=1}^{K} \alpha_k = 1
$$
</div>

由于存在和为1的这个条件，各个变量之间存在联系，所以不能简单的分别对各个 $\alpha_k$ 求偏导。如果忽略这个约束，那么每个概率都可以无限大，目标函数也无限大了。

当对其中一个变量如 $\alpha_1$ 求偏导的时候，考虑到总和为1的约束条件，此时应该把剩下的 $K-1$ 个变量看作“另一个”变量(可取其中一个变量如 $\alpha_K$ 作为代表)：

<div>
$$
\begin{aligned}
\frac{\partial Q}{\partial \alpha_1} &= \frac{\partial\{ n_1 \cdot log(\alpha_1) + \sum_{k=2}^{K} n_k \cdot log(\alpha_k)\}}
{\partial \alpha_1} \\
&= \frac{n_1}{\alpha_1} + \frac{n_K \cdot \partial log(\alpha_K)}{\alpha_1} \\
&= \frac{n_1}{\alpha_1} + \frac{n_K}{\alpha_K} \cdot \frac{\partial \alpha_K}{\alpha_1} \\
&= \frac{n_1}{\alpha_1} + \frac{n_K}{\alpha_K} \cdot \frac{\partial (1-\sum_{k=1}^{K-1} \alpha_k)}{\alpha_1} \\
&= \frac{n_1}{\alpha_1} + \frac{n_K}{\alpha_K} \cdot \frac{\partial (-\alpha_1)}{\partial \alpha_1} \\
&= \frac{n_1}{\alpha_1} - \frac{n_K}{\alpha_K}
\end{aligned}
$$
</div>

令
$$\frac{\partial Q}{\partial \alpha_1}=\frac{n_1}{\alpha_1} - \frac{n_K}{\alpha_K}=0$$

得
$$\frac{n_1}{\alpha_1} = \frac{n_K}{\alpha_K}$$

同理
$$\frac{n_1}{\alpha_1} 
=\frac{n_2}{\alpha_2}
=\cdots
=\frac{n_{K-1}}{\alpha_{K-1}}
=\frac{n_K}{\alpha_K}$$

因此
$$\frac{n_1}{\alpha_1}=\frac{n_1+\cdots+n_K}{\alpha_1+\cdots+\alpha_K}=\frac{N}{1}=N$$

因此
$$\alpha_1=\frac{n_1}{N},\cdots,\alpha_K=\frac{n_K}{N}$$

即
$$\alpha_k=\frac{n_k}{N},k=1,2,\cdots,K$$

求解完毕。

