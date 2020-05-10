# 定积分


### 一、定积分的概念与性质

1、定义：设函数 $f(x)$ 在 $[a,b]$ 上有界，在 $[a,b]$ 中任意插入若干分点 $a=x_0 < x_1 < x_2 < \cdots < x_{n-1} < x_n=b$，把区间 $[a,b]$ 分成 $n$ 个小区 $[x_0, x_1]$，$[x_1,x_2]$，$\cdots$，$[x_{n-1},x_n]$，各个小区间的长度依次为 $\Delta x_1=x_1-x_0$, $\Delta x_2=x_2-x_1$, $\cdots$, $\Delta x_n=x_n-x_{n-1}$。在每个小区间 $[x_{i-1},x_i]$ 上任取一点 $\xi_i (x_{i-1} \leq \xi \leq x_i)$，作函数值 $f(\xi)$ 与小区间长度 $\Delta x_i$ 的乘积 $f(\xi_i) \cdot \Delta x_i$，并作和

$$S=\sum_{i=1}^{n}f(\xi)\Delta x_i$$

记 $\lambda=\max \{ \Delta x_1, \Delta x_2, \cdots, \Delta x_n \}$，如果当 $\lambda \to 0$ 时，$S$ 的极限总存在，且与闭区间 $[a,b]$ 的分法及点 $\xi_i$ 的取法无关，那么称这个极限 $I$ 为函数  $f(x)$ 在区间 $[a,b]$ 上的定积分，记作 $\int_{a}^{b} f(x) dx$，即

$$\int_{a}^{b} f(x) dx=I=\lim_{\lambda \to 0} \sum_{i=1}^{n} f(\xi_i) \Delta x_i$$

其中 $f(x)$ 叫做被积函数，$f(x)dx$ 叫做被积表达式，$x$ 叫做积分变量，$a$ 叫做积分下限，$b$ 叫做积分上限，$[a,b]$ 叫做积分区间

2、定理：设 $f(x)$ 在区间 $[a,b]$ 上连续，则 $f(x)$ 在 $[a,b]$ 上可积

3、定理：设 $f(x)$ 在区间 $[a,b]$ 上有界，且只有有限个间断点，则 $f(x)$ 在 $[a,b]$ 上可积

4、性质

(1) 设 $\alpha$ 与 $\beta$ 均为常数，则

$$\int_{a}^{b}[\alpha f(x) + \beta g(x)]dx=\alpha \int_{a}^{b} f(x)dx+ \beta \int_{a}^{b} g(x) dx$$

(2) 设 $a < c < b$，则

$$\int_{a}^{b} f(x) dx=\int_{a}^{c}f(x)dx+\int_{c}^{b}f(x)dx$$

(3) 如果在区间 $[a,b]$ 上 $f(x) \equiv 1$，那么

$$\int_{a}^{b} 1 dx=\int_{a}^{b}dx=b-a$$

(4) 如果在区间 $[a,b]$ 上 $f(x) \geq 0$，那么

$$\int_{a}^{b} f(x) dx \geq 0$$

(5) 设 $M$ 及 $m$ 分别是函数 $f(x)$ 在区间 $[a,b]$ 上的最大值及最小值，则

$$m(b-a) \leq \int_{a}^{b} f(x) dx \leq M(b-a)$$

(6) 定积分中值定理：如果函数 f(x) 在积分区间 $[a,b]$ 上连续，那么在 $[a,b]$ 上至少存在一个点 $\xi$，使用下式成立：

$$\int_{a}^{b} f(x)dx=f(\xi)(b-a)$$

### 二、微积分基本公式

1、定理：如果函数 $f(x)$ 在区间 $[a,b]$ 上连续，那么积分上限的函数

$$\Phi(x)=\int_{a}^{x}f(t)dt$$

在 $[a,b]$ 上可导，并且它的导数

$$\Phi'(x)=\frac{d}{dx} \int_{a}^{b} f(t)dt=f(x)$$

2、定理：如果函数 $f(x)$ 在区间 $[a,b]$ 上连续，那么函数

$$\Phi(x)=\int_{a}^{x} f(t)dt$$

就是 $f(x)$ 在 $[a,b]$ 上的一个原函数

3、定理（牛顿莱布尼茨公式、微积分基本定理）：如果函数 $F(x)$ 是连续函数 $f(x)$ 在区间 $[a,b]$ 上的一个原函数，那么

$$\int_{a}^{b} f(x) dx=F(b)-F(a)=[F(x)]_{a}^{b}$$

这个公式表明：一个连续函数在区间 $[a,b]$ 上的定积分等于它的任一个原函数在区间 $[a,b]$ 上的增量

### 三、定积分的换元法和分部积分法

1、定理（换元公式）：假设函数 $f(x)$ 在区间 $[a,b]$ 上连续，函数 $x=\varphi(t)$ 满足条件：
(1) $\varphi(\alpha)=a, \varphi(\beta)=b$
(2) $\varphi(t)$ 在 $[\alpha,\beta]$（ 或 $[\beta,\alpha]$ ） 上具有连续导数，且其值域 $R_{\varphi}=[a,b]$，则有

$$\int_{a}^{b}f(x)dx=\int_{\alpha}^{beta}f[\varphi(t)] \cdot \varphi '(t) dt$$

应用换元公式时有两点值得注意：(1) 用 $x=\varphi(x)$ 把原来变量 $x$ 代换成新变量 $t$ 时，积分限也要换成相应于新变量 $t$ 的积分限；(2) 求出 $f[\varphi(t)] \cdot \varphi'(t)$ 的一个原函数 $\Phi(t)$ 后，不必像计算不定积分那样再要把 $\Phi(t)$ 变换成原来变量 $x$ 的函数，而只要把新变量 $t$ 的上、下限分别代入 $\Phi(t)$ 中然后相减即可

换元公式也可以反过来使用。为使用方便起见，把换元公式中左右两边对调位置，同时把 $t$ 改记为 $x$，而 $x$ 改记为 $t$， 得

$$\int_{a}^{b}f[\varphi(x)] \cdot \varphi'(x) dx=\int_{\alpha}^{\beta} f(t) dt$$

其中 $t=\varphi(x)$，$\alpha=\varphi(a)$，$\beta=\varphi(b)$

2、分部积分：

$$\int_{a}^{b} uv'dx=[uv]_{a}^{b}-\int_{a}^{b}vu'dx$$

简记为

$$\int_{a}^{b} udv=[uv]_{a}^{b}-\int_{a}^{b}vdu$$

### 四、反常积分


