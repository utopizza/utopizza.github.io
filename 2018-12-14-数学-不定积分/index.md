# 不定积分


### 一、不定积分的概念与性质

1、定义：如果在区间 $I$ 上，可导函数 $F(x)$ 的导函数为 $f(x)$，即对任一 $x \in I$，都有 $F'(x)=f(x)$ 或 $dF(x)=f(x)dx$，那么函数 $F(x)$ 就称为 $f(x)$（或 $f(x)dx$）在区间 $I$ 上的一个原函数

2、定理：如果函数 $f(x)$ 在区间 $I$ 上连续，那么在区间 $I$ 上存在可导函数 $F(x)$，使得对任一 $x \in I$ 都有 $F'(x)=f(x)$，也就是说连续函数一定有原函数

3、定义：在区间 $I$ 上，函数 $f(x)$ 的带有任意常数项的原函数称为 $f(x)$ （或 $f(x)dx$）在区间 $I$ 上的不定积分，记作 $\int f(x)dx$。其中记号 $\int$ 称为积分号，$f(x)$ 称为被积函数，$f(x)dx$ 称为被积表达式，$x$ 称为积分变量

4、由于 $\int f(x)dx$ 是 $f(x)$ 的原函数，所以 $\frac{d}{dx}[\int f(x)dx]=f(x)$ 或 $d[\int f(x)dx]=f(x)dx$；又由于 $F(x)$ 是 $F'(x)$  的原函数，所以 $\int F'(x) dx=F(x)+C$ 或 $\int dF(x)=F(x)+C$。由此可见，微分运算（以记号 $d$ 表示）与积分运算（以记号 $\int$ 表示）是互逆的。当此两个记号连在一起时，或者抵消，或者抵消后差一个常数。

$$\int F'(x) dx = \int dF(x)$$

5、既然积分运算是微分运算的逆运算，那么很自然地可以从导数公式得到相应的积分公式。基本积分表：

 - $\int k dx=kx+C$ ($k$ 是常数)
 - $\int x^\mu dx=\frac{x^{\mu+1}}{\mu+1}+C$ ($\mu \neq -1$)
 - $\int \frac{1}{x} dx=\ln|x|+C$
 - $\int \frac{1}{1+x^2} dx=\arctan x +C$
 - $\int \frac{1}{\sqrt{1-x^2}} dx=\arcsin x+C$
 - $\int \cos x dx=\sin x+C$
 - $\int \sin x dx=- \cos x +C$
 - $\int \frac{1}{\cos^{2} x} dx=\int \sec^2 x dx=\tan x+C$
 - $\int \frac{1}{\sin^{2} x} dx=\int \csc^2 x dx=- \cot x+C$
 - $\int \sec x \tan x dx=\sec x+C$
 - $\int \csc x \cot x dx=- \csc x+C$
 - $\int e^x dx=e^x+C$
 - $\int a^x dx=\frac{a^x}{\ln a}+C$
 
6、性质1：设函数 $f(x)$ 及 $g(x)$ 的原函数存在，则

$$\int [f(x)+g(x)]dx=\int f(x)dx + \int g(x)dx$$

7、性质2：设函数 $f(x)$ 的原函数存在，$k$ 为非零常数，则

$$\int k \cdot f(x)dx = k \cdot \int f(x)dx$$

### 二、换元积分法

1、定理1：设 $f(x)$ 具有原函数，$u=\varphi(x)$ 可导，则有换元公式

$$\int f[\varphi(x)] \cdot \varphi'(x) dx=[\int f(u) du]_{u=\varphi(x)}$$

2、定理2：设 $x=\psi(t)$ 是单调的可导函数，并且 $\psi(t) \neq 0$。又设 $f[\psi(t)] \cdot \psi(t)$ 具有原函数。则有换元公式

$$\int f(x) dx=[\int f[\psi(t)] \cdot \psi'(t) dt]_{t=\psi^{-1}(x)}$$

其中 $\psi^{-1}(x)$ 是 $x=\psi(t)$ 的反函数

3、基本积分表续（以下 $a > 0$）

 - $\int \tan x dx=-\ln |\cos x|+C$
 - $\int \cot x dx=\ln |\sin x|+C$
 - $\int \sec x dx=\ln |\sec x+\tan x|+C$
 - $\int \csc x dx=\ln |\csc x-\cot x|+C$
 - $\int \frac{1}{a^2+x^2} dx=\frac{1}{a} \arctan \frac{x}{a}+C$
 - $\int \frac{1}{x^2-a^2} dx=\frac{1}{2a} \ln |\frac{x-a}{x+a}|+C$
 - $\int \frac{1}{\sqrt{a^2-x^2}} dx=\arcsin \frac{x}{a}+C$
 - $\int \frac{1}{\sqrt{a^2+x^2}} dx=\ln(x+\sqrt{a^2+x^2})+C$
 - $\int \frac{1}{\sqrt{x^2-a^2}} dx=\ln |x+\sqrt{x^2-a^2}|+C$
 
### 三、分部积分法

设函数 $u=u(x)$ 及 $v=v(x)$ 具有连续导数，则两个函数乘积的导数公式为

$$(uv)'=u'v+uv'$$

移项，得

$$uv'=(uv)'-u'v$$

两边同时求不定几积分，得

$$\int uv' dx=uv-\int u'v dx$$

也可以写成

$$\int udv=uv-\int vdu$$

### 四、有理函数的积分

1、定义：两个多项式子的商 $\frac{P(x)}{Q(x)}$ 称为有理函数，又称有理分式（假定分子多项式与分母之间没有公因式）。当分子多项式 $P(x)$ 的次数小于分母多项式 $Q(x)$ 的次数时，称这有理函数为真分式，否则为假分式。

2、对于假分式，利用多项式除法，总可以将一个假分式化成一个多项式与一个真分式之和的形式

3、对于真分式 $\frac{P(x)}{Q(x)}$，如果分母可分解为两个多项式的乘积 $Q(x)=Q_1(x)Q_2(x)$ 且 $Q_1(x)$ 与 $Q_2(x)$ 没有公因式，那么它可拆分为两个真分式之和

$$\frac{P(x)}{Q(x)}=\frac{P_1(x)}{Q_1(x)}+\frac{P_2(x)}{Q_2(x)}$$

上述步骤称为把真分式化成部分分式之和。如果 $Q_1(x)$ 或 $Q_2(x)$ 还能分解成两个没有公因式的多项式的乘积，那么如此继续拆分。最后，有理函数的分解式中只出现多项式、$\frac{P_1(x)}{(x-a)^k}$、$\frac{P_2(x)}{(x^2+px+q)^l}$ 等三类函数，在进行求解。

### 五、积分表的使用

