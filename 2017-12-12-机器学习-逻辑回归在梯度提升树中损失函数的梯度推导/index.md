# 逻辑回归在梯度提升树中损失函数的梯度推导


一、梯度提升树

因为做比赛最近用到了 LightGBM 来实现逻辑回归，并尝试修改 LightGBM 的目标函数，于是顺便温习一下梯度提升树和逻辑回归，并简单推导了一下逻辑回归在梯度提升树中的损失函数的梯度计算。

前面的博文介绍过，当损失函数是平方损失和指数损失时，前向加法模型的提升树（回归提升树）可以很方便地进行目标函数的优化并构造决策树。但对于一般的损失函数，前向加法提升树就比较难实现优化。这时就需要使用梯度提升模型的提升树，它能适应一般的损失函数。类似最速下降法，梯度提升树利用损失函数的负梯度在当前模型的值

$$- \left[ \frac{\partial L(y,f(x_i))}{\partial f(x_i)} \right]$$

作为回归提升树算法中的残差的近似值，来拟合一个回归树。


二、逻辑回归

逻辑回归是比较简单而且应用非常广泛的机器学习算法。它主要用于二分类任务，但是与一般的分类器不同，逻辑回归并不直接给出未知样本的预测类别，而是给出属于某个类别的概率。

对一个二分类任务，每个训练样本都属于且只属于两种类别之中的一种，即要么是正样本，要么是负样本。习惯上，一般用 $0$ 表示负样本，用 $1$ 表示正样本。

设训练集一共有 $m$ 个样本，每个样本有 $n$ 个属性，第 $i$ 个训练样本表示为 $x_i=(x_{i}^{(1)},x_{i}^{(2)},\cdots,x_{i}^{(n)})$，$1 \leq i \leq m$。对应地，每个样本有一个类别，设为 $Y$，则对第 $i$ 个样本，要么 $Y_i=1$，要么 $Y_i=0$。

现在的任务是，给定 $m$ 个已知对应类别的样本 $\{(x_1,Y_1),(x_2,Y_2),\cdots,(x_m,Y_m)\}$，用来作为训练集，学习一个分类器模型 $f(x,Y)$，然后用这个模型来对未知类别的样本如 $(x_{m+1},？)$ 进行预测，预测它的类别 $Y_{m+1}$ 是等于 $1$ 还是等于 $0$。

逻辑回归使用 $sigmod$ 函数作为预测模型，它不直接判定某个样本是正样本还是负样本，而是给出一个条件概率，即在已知样本的 $n$ 个属性的情况下，该样本属于正样本或负样本的概率。数学描述为

$$P(Y=1 \mid x)=\frac{1}{1+e^{-w \cdot x}}$$

$$P(Y=0 \mid x)=1-\frac{1}{1+e^{-w \cdot x}}$$

其中 $w$ 是 $n$ 维的向量，每一维对应样本 $x$ 的 $n$ 个特征，可以理解为：$w^{(i)}$ 表示训练样本 $x$ 第 $i$ 个特征 $x^{(i)}$ 的权重。$w \cdot x$ 为两个向量的内积，即 

$$w \cdot x=w^{(1)}*x^{(1)}+w^{(2)}*x^{(2)}+\cdots+w^{(n)}*x^{(n)}$$

如果给定了训练集，那么模型训练的任务就是学习上面的权重参数 $w$，如果找到某个确定的向量 $w=w^{*}$ 能把训练样本最正确地分类，那么它就是我们要找的那个最优解。确定这个参数后，模型也就完全确定了。当使用该模型执行预测任务时，只需要把未知样本输入这个模型，即可得出未知样本属于正样本或负样本的概率。

那么我们如何找到这个最优的向量 $w^{*}$ ？对于逻辑回归，因为它是概率问题，所以一般使用极大似然估计法来估计模型参数。

极大似然估计法思路：“存在即合理”，使得每个已知样本（即训练样本）出现的概率最大的参数 $w^{*}$ 就是最优的参数。设给定的训练集为 $T=\{(x_1,Y_1),(x_2,Y_2),\cdots,(x_m,Y_m)\}$，其中 $x_i \in R^n$，$Y_i \in \{0,1\}$，由逻辑回归的定义知每个样本（作为正样本或负样本）出现的概率为

<div>
$$
P(x_i)=
\begin{cases}
P(Y_i=1 \mid x_i)=\frac{1}{1+e^{-w \cdot x_i}}  \\
P(Y_i=0 \mid x_i)=1-\frac{1}{1+e^{-w \cdot x_i}}
\end{cases}
$$
</div>

有了每个样本的概率 $P(x_i)$，现在只要找到一个 $w=w^{*}$ 使得所有样本的概率同时最大，应该怎么做呢？思路很简单，因为目标是使它们同时最大，因此把它们全部乘起来，得到一个关于 $w$ 的函数，只要使这个函数的值最大，那就相当于使这些概率同时最大了。这个函数称为似然函数，设为 $L(w)$，即

$$L(w)=P(x_1) * P(x_2) * \cdots * P(x_m)=\prod_{i=1}^{m} P(x_i)$$

到这里其实已经基本完成学习任务的数学定义了。但是为了能让机器执行学习任务，还需要解决两个小问题，一是公式的推导化简，二是使用什么方法搜索 $w$ 的解空间。

对于第一个问题，当你尝试把 $P(x_i)$ 的公式代入似然函数 $L(w)$ 时，你就会发现非常困难，因为 $P(x_i)$ 公式针对 $Y_i$ 分了两种情况。这里用到了一个小技巧，可以把两种情况的公式在形式上统一到一个公式。如果你注意到，对于每个样本的类别属性 $Y_i$，它要么是 $1$ 要么是 $0$ ，那么就可以利用这个性质，把两种情况统一写成：

<div>
$$
\begin{aligned}
P(x_i) 
&=P(Y_i=1 \mid x_i)^{Y_i} * P(Y_i=0 \mid x_i)^{1-Y_i} \\
&=(\frac{1}{1+e^{-w \cdot x_i}})^{Y_i} * (1-\frac{1}{1+e^{-w \cdot x_i}})^{1-Y_i}
\end{aligned}
$$
</div>

这个形式的 $P(x_i)$ 和上面的原始定义是完全等价的。你可以发现，当 $Y_i=1$ 时，乘号右边的部分因为指数为 $1-Y_i=0$ 就变成了 $1$ 而只剩下左边的部分，这就相当于当 $Y_i=1$ 自动时把 $P(Y_i=1 \mid x_i)$ 这部分选择出来了，反之亦然。

有了如此统一的 $P(x_i)$ 的表达形式，就可以代入似然函数得到

$$L(w)=\prod_{i=1}^{m} P(x_i)=\prod_{i=1}^{m} \left[ (\frac{1}{1+e^{-w \cdot x_i}})^{Y_i} * (1-\frac{1}{1+e^{-w \cdot x_i}})^{1-Y_i} \right]$$

如果进一步考虑到，因为 $P(x_i)$ 是一个概率，如果样本数目太多，大量的小数连乘对于机器来说容易出现下溢等等的精度问题，为避免这些实际问题一般对似然函数取对数，把连乘变成连加，从而得到对数似然函数

$$\log L(w)=\sum_{i=1}^{m} \left[ Y_i*\log(\frac{1}{1+e^{-w \cdot x_i}})+(1-Y_i)*(1-\frac{1}{1+e^{-w \cdot x_i}}) \right]$$

现在只要让机器找到一个 $w=w^{*}$ 使得 $\log L(w)$ 最大，也即极大值，那么这就是最优的解。对第二个问题，一般常用的方法是梯度下降法或者拟牛顿法来搜索 $w$ 的解空间，这里限于篇幅不再展开。

三、逻辑回归之于梯度提升树

现在有了逻辑回归的目标函数，就可以推导出逻辑回归的损失函数在梯度提升树中的每一轮训练时的负梯度的值。

首先为了在形式上与第一节中的梯度提升树的负梯度公式保持统一，我们把上面逻辑回归的目标函数 $\log L(w)$ 写成**关于每一个样本的损失函数**的形式

$$L(Y_i,P(x_i))=Y_i*\log(\frac{1}{1+e^{-w \cdot x_i}})+(1-Y_i)*(1-\frac{1}{1+e^{-w \cdot x_i}})$$

为了在接下来的推导过程简便表达，先设

$$z=\frac{1}{1+e^{-w \cdot x_i}}, \quad 1-z=1-\frac{1}{1+e^{-w \cdot x_i}}$$

为了简洁过程在这里我们把 $-w*x_i$ 看作一个整体的变量即可，现在分别对 $z$ 和 $1-z$ 求导，有

$$z'=\frac{e^{-w \cdot x_i}}{(1+e^{-w \cdot x_i})^2}, \quad
(1-z)'=-\frac{e^{-w \cdot x_i}}{(1+e^{-w \cdot x_i})^2}
$$

因此梯度提升树在第 $i$ 个样本处的负梯度（一阶导）：

<div>
$$
\begin{aligned}
-\frac{\partial L(Y_i,P(x_i))}{\partial P(x_i)} 
&= -\left[ Y_i * \log(z) + (1-Y_i) * \log(1-z) \right]' \\
&= -\left[ Y_i * \frac{1}{z} * z' + (1-Y_i) * \frac{1}{1-z} * (1-z)' \right]\\
&= -\left[ Y_i * (1+e^{-w \cdot x}) * \frac{e^{-w \cdot x_i}}{(1+e^{-w \cdot x_i})^2} + (1-Y_i) * \frac{1+e^{-w \cdot x}}{e^{-w \cdot x}} * -\frac{e^{-w \cdot x_i}}{(1+e^{-w \cdot x_i})^2} \right]\\
&= -\left[ Y_i * (1-\frac{1}{1+e^{-w \cdot x_i}}) + (1-Y_i) * -\frac{1}{1+e^{-w \cdot x_i}} \right]\\
&= -\left[ Y_i - Y_i * \frac{1}{1+e^{-w \cdot x_i}} - \frac{1}{1+e^{-w \cdot x_i}} + Y_i * \frac{1}{1+e^{-w \cdot x_i}} \right]\\
&= -\left[ Y_i - \frac{1}{1+e^{-w \cdot x_i}} \right] \\
&= \frac{1}{1+e^{-w \cdot x_i}} - Y_i
\end{aligned}
$$
</div>

进一步，在 LightGBM 中自定义训练的目标函数的时候需要计算二阶导：

<div>
$$
\begin{aligned}
\frac{\partial^2 L(Y_i,P(x_i))}{\partial P(x_i)} 
&= (\frac{1}{1+e^{-w \cdot x_i}} - Y_i)' \\
&= \frac{e^{-w \cdot x_i}}{(1+e^{-w \cdot x_i})^2} \\
&= \frac{1}{1+e^{-w \cdot x_i}} * \frac{e^{-w \cdot x_i}}{1+e^{-w \cdot x_i}} \\
&= \frac{1}{1+e^{-w \cdot x_i}} * (1-\frac{1}{1+e^{-w \cdot x_i}})
\end{aligned}
$$
</div>

关于设置自定义目标函数和验证函数，lightGBM 的官方示例代码：

```
# self-defined objective function
# f(preds: array, train_data: Dataset) -> grad: array, hess: array
# log likelihood loss
def loglikelood(preds, train_data):
    labels = train_data.get_label()
    preds = 1. / (1. + np.exp(-preds))
    grad = preds - labels
    hess = preds * (1. - preds)
    return grad, hess
    
# self-defined eval metric
# f(preds: array, train_data: Dataset) -> name: string, value: array, is_higher_better: bool
# binary error
def binary_error(preds, train_data):
    labels = train_data.get_label()
    return 'error', np.mean(labels != (preds > 0.5)), False

# train model
gbm = lgb.train(params,
                lgb_train,
                num_boost_round=10,
                init_model=gbm,
                fobj=loglikelood,
                feval=binary_error,
                valid_sets=lgb_eval)
```

参考：

 1. 《统计学习方法》 李航
 2. 《Machine Learning》 Andrew NG, Coursera
 3. [微软 LightGBM 官方完整代码示例][1]
 4. [关于Xgboost的自定义目标函数的相关问题][2]


  [1]: https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide
  [2]: https://github.com/dmlc/xgboost/issues/15

