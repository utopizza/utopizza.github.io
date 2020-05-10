# 线性变换的矩阵


我们知道，线性空间中，在给定基的情况下，对任意一个向量 $\vec{v}$ 施予一个线性变换 $T$ 时，就让该向量的坐标向量左乘某个矩阵 $A$ 即可。例如，设在线性空间 $V_n(F)$ 中，取一组基为 $\{\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}\}$，向量 $\vec{w}$ 在该基下的坐标为 $\vec{Y}$，向量 $\vec{v}$ 在该基下的坐标为 $\vec{X}$，即：

$$\vec{w}=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \vec{Y}$$

$$\vec{v}=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \vec{X}$$

如果向量 $\vec{w}$ 是由向量 $\vec{v}$ 经过线性变换 $T$ 得到的，即

$$\vec{w}=T(\vec{v})$$

那么它们的坐标关系为：

$$\vec{Y}=A \vec{X}$$

并且，线性空间中任一个线性变换 $T$ 可以由某个矩阵 $A$ 唯一对应：

$$T \iff A$$

为什么？这个矩阵 $A$ 如何得到？接下来复习一下 $A$ 的推导。需要的预备知识：

 - 线性空间定义
 - 线性空间的基与坐标
 - 线性变换定义

---

记 $T$ 为线性空间 $V_n(F)$ 上的线性变换。记 $\{\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}\}$ 为 $V_n(F)$ 的一组基，则对任意向量 $\forall \pmb\beta \in V_n(F)$，由基的定义可得

<div>
$$\vec{v}=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \vec{X}=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \begin{pmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{pmatrix} =x_1\vec{\alpha_1}+x_2\vec{\alpha_2}+\cdots+x_n\vec{\alpha_n}$$
</div>

对 $\vec{v}$ 施予线性变换 $T$：

<div>
$$
\begin{aligned}
T(\vec{v})
&=T(x_1\vec{\alpha_1}+x_2\vec{\alpha_2}+\cdots+x_n\vec{\alpha_n}) \\
&=T(x_1\vec{\alpha_1})+T(x_2\vec{\alpha_2})+\cdots+T(x_n\vec{\alpha_n}) \\
&=x_1T(\vec{\alpha_1})+x_2T(\vec{\alpha_2})+\cdots+x_nT(\vec{\alpha_n}) \\
&=(T(\vec{\alpha_1}),T(\vec{\alpha_2}),\cdots,T(\vec{\alpha_n})) \begin{pmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{pmatrix}
\end{aligned}
$$
</div>

到这里可以清楚看到，对向量 $\vec{v}$ 施予线性变换 $T$，可以转化为对基 $\{ \vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n} \}$ 施予线性变换，并且在基变换后，向量 $\vec{v}$ 的坐标保持不变！因此，对空间中任意向量的线性变换，就转化到了该空间下一组基到另一组“基”的线性变换（注意，由于实际上线性变换只能保持线性相关，不能保持线性无关，因此无法保证对基进行线性变换后的向量组 $\{ T(\vec{\alpha_1}),T(\vec{\alpha_2}),\cdots,T(\vec{\alpha_n}) \}$ 是否仍能作为一组基。准确来讲，$T(\vec{\alpha\_{i}})$ 只能是原基的“像”。但为了便于陈述和理解，暂且称这组“像”为“新基”）。

那么接下来的问题是，对基 $\{ \vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n} \}$ 施予线性变换 $T$ 后，“新基” $\{ T(\vec{\alpha_1}),T(\vec{\alpha_2}),\cdots,T(\vec{\alpha_n}) \}$ 与原基的关系是怎么样的？

---

我们知道，对一个向量施予线性变换后仍是一个向量，因此“新基”中的每个元素也仍是一个向量。既然是向量，就一定可以用基和坐标来表示。那么我们就尝试用原基来表示“新基”中的每一个向量吧（因为我们希望知道“新基”与原基的关系），假设向量 $T(\vec{\alpha_1}),T(\vec{\alpha_2}),\cdots,T(\vec{\alpha_n})$ 在原基 $(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n})$ 下的表达分别为：

<div>
$$T(\vec{\alpha_1})=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \begin{pmatrix} x_{11} \\ x_{21} \\ \vdots \\ x_{n1} \end{pmatrix}$$
</div>

<div>
$$T(\vec{\alpha_2})=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \begin{pmatrix} x_{12} \\ x_{22} \\ \vdots \\ x_{n2} \end{pmatrix}$$
</div>

$$\cdots$$

<div>
$$T(\vec{\alpha_n})=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \begin{pmatrix} x_{1n} \\ x_{2n} \\ \vdots \\ x_{nn} \end{pmatrix}$$
</div>

把以上式子写到一起：

<div>
$$(T(\vec{\alpha_1}),T(\vec{\alpha_2}),\cdots,T(\vec{\alpha_n}))=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n})
\begin{pmatrix}
x_{11} & x_{12} & \cdots & x_{1n} \\
x_{21} & x_{22} & \cdots& x_{2n} \\
\vdots & \vdots & \ddots & \vdots \\
x_{n1} & x_{n2} & \cdots & x_{nn} \\
\end{pmatrix}
$$
</div>

为简便表达，用 $A$ 来表达矩阵 $[x_{ij}]$，因此

$$(T(\vec{\alpha_1}),T(\vec{\alpha_2}),\cdots,T(\vec{\alpha_n}))=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) A$$

这就是线性变换后 “新基” $\{ T(\vec{\alpha_1}),T(\vec{\alpha_2}),\cdots,T(\vec{\alpha_n}) \}$ 与原基 $\{ \vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n} \}$ 的关系表达式。我们称矩阵 $A$ 为变换矩阵。

从上述过程可以知道，变换矩阵 $A$ 的第 $i$ 列就是原基中第 $i$ 个向量 $\vec{\alpha_i}$ 的像 $T(\vec{\alpha_i})$ 在原基下的坐标表示，坐标的唯一性决定了 $A$ 的唯一性。也就是说，只要选定了一组基，线性变换 $T$ 对这组基的作用（“新基”的坐标），在这组基下是唯一的。因此，在选定了一组基的情况下，线性变换 $T$ 与矩阵 $A$ 是一一对应的，即：

$$T \iff A $$

---

回到本文开头：

$$\vec{w}=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \vec{Y}$$

$$\vec{v}=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) \vec{X}$$

如果向量 $\vec{w}$ 是由向量 $\vec{v}$ 经过线性变换 $T$ 得到的，即有

$$\vec{w}=T(\vec{v})=(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n}) A \vec{X}$$

通过对比，显然有：

$$\vec{Y}=A \vec{X}$$

特别地，当基 $(\vec{\alpha_1},\vec{\alpha_2},\cdots,\vec{\alpha_n})$ 为 $R^n$ 中的自然基，即 

<div>
$\vec{\alpha_1}= \begin{pmatrix} 1 \\ 0 \\ \vdots \\ 0 \end{pmatrix}$，$\vec{\alpha_2}= \begin{pmatrix} 0 \\ 1 \\ \vdots \\ 0 \end{pmatrix}$，$\cdots$，$\vec{\alpha_n}=\begin{pmatrix} 0 \\ 0 \\ \vdots \\ 1 \end{pmatrix}$ 
</div>

时，有

$$\vec{w}=\vec{Y}$$

$$\vec{v}=\vec{X}$$

此时有

$$\vec{w}=T(\vec{v})=A \vec{v}$$



