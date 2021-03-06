# 函数与极限


### 一、映射与函数

1、映射：设 $X$、$Y$ 是两个非空集合，如果存在一个法则 $f$，使得对 $X$ 中每个元素 $x$，按法则 $f$，在 $Y$ 中有唯一确定的元素 $y$ 与之对应，那么称 $f$ 为从 $X$ 到 $Y$ 的映射，记作 $f:X \to Y$，其中 $y$ 称为元素 $x$ 在映射 $f$ 下的像，记作 $y=f(x)$，而 $x$ 称为元素 $y$ 在映射 $f$ 下的一个原像。集合 $X$ 称为映射 $f$ 的定义域，$X$ 中所有元素的像组成的集合称为映射 $f$ 的值域。

2、函数：设数集 $D \subset R$，$R$ 为实数集，则称映射 $f:D \to R$ 为定义在 $D$ 上的函数，记作 $y=f(x), x \in D$，其中 $x$ 称为自变量，$y$ 称为因变量，$D$ 称为定义域。

五类基本初等函数：
1. 幂函数：$y=x^{\mu}$ ( $\mu \in R$ 是常数 )
2. 指数函数：$y=a^{x}$ ( $a>0$ 且 $a \neq 1$ )
3. 对数函数：$y=\log_ax$ ( $a>0$ 且 $a \neq 1$ )
4. 三角函数：$y=\sin x$，$y=\cos x$，$y=\tan x$ 等等
5. 反三角函数：$y=\arcsin x$，$y=\arccos x$，$y=\arctan x$ 等等

### 二、数列的极限

1、定义：设 {$x_n$} 为一数列，如果存在常数 $a$，对于任意给定的正数 $\varepsilon$（不论它多么小），总存在正整数 $N$，使得当 $n>N$ 时，不等式 $|x_n - a| < \varepsilon$ 都成立，那么称常数 $a$ 是数列 $x_n$ 的极限，或者称数列 $x_n$ 收敛于 $a$，记为 $\lim_{n \to \infty} x_n=a$。上述定义可简记为：
$$\lim_{n \to \infty} x_n=a \iff \forall \varepsilon>0, \exists N \in N^{+}, \text{ 当 } n>N \text{ 时 }, \text{ 有 } |x_n-a|<\varepsilon $$

2、定理（数列极限唯一性）：如果数列 $x_n$ 收敛，那么它的极限唯一

3、定理（收敛数列有界性）：如果数列 $x_n$ 收敛，那么数列 $x_n$ 一定有界

4、定理（收敛数列保号性）：如果 $\lim_{n \to \infty}x_n=a$，且 $a>0$（或 $a < 0$），那么存在正整数 $N$，当 $n>N$ 时，都有 $x_n>0$（或 $x_n < 0$）

5、定理（子数列收敛性）：如果数列 $x_n$ 收敛于 $a$，那么它的任一子数列也收敛于 $a$

### 三、函数的极限

1、定义(1)：设函数 $f(x)$ 在点 $x_0$ 的某一点去心邻域内有定义。如果存在常数 $A$，对于任意给定的正数 $\varepsilon$（不论它多么小），总存在整数 $\delta$，使得当 $x$ 满足不等式 $0<|x-x_0|<\delta$ 时，对应的函数值 $f(x)$ 都满足不等式 $|f(x)-A|<\varepsilon$，那么常数 $A$ 就叫做函数 $f(x)$ 当 $x \to x_0$ 时的极限，记作 $\lim_{x \to x_0}f(x)=A$。定义可简记为：
$$\lim_{x \to x_0}f(x)=A \iff \forall \varepsilon>0, \exists \delta>0, \text{ 当 } 0<|x-x_0|<\delta \text{ 时 }, \text{ 有 } |f(x)-A|<\varepsilon$$

2、定义(2)：设函数 $f(x)$ 当 $|x|$ 大于某一正数时有定义。如果存在常数 $A$，对于任意给定的正数 $\varepsilon$（不论它多么小），总存在着正数 $X$，使得当 $x$ 满足不等式 $|x|>X$ 时，对应的函数值 $f(x)$ 都满足不等式 $|f(x)-A|<\varepsilon$，那么常数 $A$ 就叫做函数 $f(x)$ 当 $x \to \infty$ 时的极限，记作 $\lim_{x \to \infty} f(x)=A$。定义可简记为：
$$\lim_{x \to \infty}f(x)=A \iff \forall \varepsilon>0, \exists X>0, \text{ 当 } |x|>X \text{ 时 }, \text{ 有 } |f(x)-A|<\varepsilon$$

3、定理（函数极限唯一性）：如果 $\lim_{x \to x_0}f(x)$ 存在，那么这极限唯一

4、定理（函数极限局部有界性）：如果 $\lim_{x \to x_0}f(x)=A$，那么存在常数 $M>0$ 和 $\delta >0$，使得当 $0<|x-x_0|<\delta$ 时，有 $|f(x)| \leq M$

5、定理（函数极限局部保号性）：如果 $\lim_{x \to x_0}f(x)=A$ 且 $A>0$（或 $A < 0$），那么存在常数 $\delta >0$，使得当 $0<|x-x_0|<\delta$ 时，有 $f(x)>0$（或 $f(x)< 0$）

### 四、无穷小与无穷大

1、无穷小：如果函数 $f(x)$ 当 $x \to x_0$（或 $x \to \infty$）时的极限为零，那么称函数 $f(x)$ 为当 $x \to x_0$（或 $x \to \infty$）时的无穷小

2、无穷大：设函数 $f(x)$ 在 $x_0$ 的某一去心邻域内有定义（或 $|x|$ 大于某一正数时有定义）。如果对于任意给定的正数 $M$（不论它多大），总存在正数 $\delta$（或正数 $X$），只要 $x$ 适合不等式 $0<|x-x_0|<\delta$（或 $|x|>X$），对应的函数值 $f(x)$ 总满足不等式 $|f(x)|>M$，那么称函数 $f(x)$ 是当 $x \to x_0$（或 $x \to \infty$）时的无穷大

3、定理：在自变量的同一变化过程 $x \to x_0$（或 $x \to \infty$）中，函数 $f(x)$ 具有极限 $A$ 的充分必要条件是 $f(x)=A+\alpha$，其中 $\alpha$ 是无穷小

4、定理：在自变量的同一变化过程中，如果 $f(x)$ 为无穷大，那么 $\frac{1}{f(x)}$ 为无穷小；反之，如果 $f(x)$ 为无穷小，且 $f(x) \neq 0$，那么 $\frac{1}{f(x)}$ 为无穷大

### 五、极限运算法则

1、定理：有限个无穷小的和是无穷小

2、定理：有界函数与无穷小的乘积是无穷小
 - 常数与无穷小的乘积是无穷小
 - 有限个无穷小的乘积是无穷小

3、定理：如果 $\lim f(x)=A$，$\lim g(x)=B$，那么
 - $\lim [f(x) \pm g(x)]=\lim f(x) \pm \lim g(x) = A \pm B$
 - $\lim [f(x) \cdot g(x)]=\lim f(x) \cdot \lim g(x) = A \cdot B$
 - 若 $B \neq 0$，则 $\lim \frac{f(x)}{g(x)}=\frac{\lim f(x)}{\lim g(x)}=\frac{A}{B}$
 - $\lim [cf(x)]=c \lim f(x)$
 - $\lim [f(x)]^n=[\lim f(x)]^n$

4、数列极限的四则运算法则同上

5、定理：如果 $f(x) \geq g(x)$，而 $\lim f(x)=A$，$\lim g(x)=B$，那么 $A \geq B$

6、定理：设函数 $y=f[g(x)]$ 是由函数 $u=g(x)$ 与 $y=f(u)$ 复合而成，$f[g(x)]$ 在点 $x_0$ 的某去心邻域内有定义，若 $\lim_{x \to x_0}=u_0$，$lim_{u \to u_0}=A$，且存在 $\delta >0$，当 $x \in U(x_0,\delta_0)$ 时，有 $g(x) \neq u_0$，则 $lim_{x \to x_0}f[g(x)]=\lim_{u \to u_0}f(u)=A$

### 六、极限存在准则

1、准则(I)-数列：如果数列 {$x_n$}，{$y_n$} 及 {$z_n$} 满足：
(i) 从某项起，即 $\exists n_0 \in N^{+}$，当 $n>n_0$  时，有 $y_n \leq x_n \leq z_n$
(ii) $\lim_{n \to \infty} y_n=a$，$\lim_{n \to \infty}z_n=a$
那么数列 {$x_n$} 的极限存在，且 $\lim_{n \to \infty}x_n=a$

2、准则(I)-函数：如果
(i) 当 $x \in U(x_0, r)$ （或 $|x|>M$）时，$g(x) \leq f(x) \leq h(x)$
(ii) $\lim_{x \to x_0/\infty} g(x)=A$，$\lim_{x \to x_0/\infty}h(x)=A$
那么 $\lim_{x \to x_0/\infty} f(x)=A$

3、准则(II)-数列：单调有界函数必有极限

4、准则(II)-函数：设函数 $f(x)$ 在点 $x_0$ 的某个左邻域内单调并且有界，则 $f(x)$ 在 $x_0$ 的左极限 $f(x_0^{-})$ 必定存在
  
5、柯西极限存在准则：数列 {$x_n$} 收敛的充分必要条件是：对于任意给定的正数 $\varepsilon$，存在正整数 $N$，使得当 $m>N$，$n>N$ 时，有 $|x_n-x_m| < \varepsilon$

### 七、无穷小的比较

### 八、函数的连续性

1、定义：设函数 $y=f(x)$ 在点 $x_0$ 的某一邻域内有定义，如果 
$$\lim_{\Delta x \to 0} \Delta y = \lim_{\Delta x \to 0}[f(x_0+\Delta x)-f(x_0)]=0 $$
，那么就称函数 $y=f(x)$ 在点 $x_0$ 连续

2、定义：设函数 $y=f(x)$ 在点 $x_0$ 的某一邻域内有定义，如果 
$$\lim_{x \to x_0} f(x)=f(x_0)$$
，那么就称函数 $f(x)$ 在点 $x_0$ 连续

### 九、连续函数的运算与初等函数的连续性

### 十、闭区间上连续函数的性质

