
---
title: "LaTeX"
---

## 数据公式

$$
\tag*{(1)} P(E) = {n \choose k} p^k (1-p)^{n-k}
$$

```math

基础符号
\lbrace \{
\rbrace \}
\theta
\alpha
\beta
\delta
\omega
\phi
\psi
\lambda
\mu

上缀符号

\hat{a}
\check{a}
\breve{a}
\tilde{a}
\bar{a}
\vec{a}
\acute{a}
\grave{a}
\mathring{a}
\dot{a}
\ddot{a}

基础运算
\pm
\mp
\times
\cdot
\ast
\div
\frac ab \cfrac ab
\frac{a}{a + b}
{x+y} \over {y+z}
{x^5}^6
\lfoor{x}
\rfoor{x + y}
\lceil

逻辑运算
=
>
<
\geq
\leq
\neq
\ngeq \not\geq
\nleq \not\leq
\approx
\equiv

集合运算
\emptyset
\mathbb{Z}
\mathbb{R}
\in
\notin \not\in
\subset
\supset
\subseteq
\subsetneq
\supseteq
\supsetneq
\not\subset
\not\supset
\cup \bigcup
\cap \bigcap
\setminus
\bigodot
\bigotimes

排列组合
{n+1 \choose k}
\binom{n+1}{k}
{n+1 \atop k}
\pmod a
\sum_{i=1}^n
\prod_{i=1}^n{a+b}
\int_{i=1}^\infty
\int_{i=1}^\infty

数学符号
\infty
\imath
\jmath

箭头
\uparrow
\Uparrow
\downarrow
\Downarrow
\leftarrow
\Leftarrow
\rightarrow
\Rightarrow

\ldots
\cdots
\vdots
\ddots
\underline{x}
\tag{1}
\overbrace{a + b}^{2.0}
\underbrace{a + b + c}_{1.0}

高级运算
\overline{xy}
\sqrt{x}
\sqrt[3]{x+y}
\log{x}
\ln{x}
\log_2 10
\sin x
\partial{x} \partial^2 u
y(x)=x^3+2x^2+x+1
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt
y_k=\varphi(u_k+v_k)
x^{y}=(1+{\rm e}^x)^{-2xy}
\displaystyle f(n)=\sum_{i=1}^{n}{n*(n+1)}
\iint dx dy=\sigma
\left. \frac{{\rm d}y}{{\rm d}x} \right|_{x=0}=3x+1=1

矩阵
\begin{matrix}
1 & x & x^2 \\
1 & y & y^2 \\
1 & z & z^2 \\
\end{matrix}

\begin{pmatrix}
1 & x & x^2 \\
1 & y & y^2 \\
1 & z & z^2 \\
\end{pmatrix}

带括号的矩阵 pmatrix 圆弧 bmatrix 方括号 Bmatrix 花括号 vmatrix 竖线 Vmatrix 双竖线

省略元素矩阵
\begin{bmatrix}
1 & a_1 & a_1^2 & \cdots & a_1^n \\
1 & a_2 & a_2^2 & \cdots & a_2^n \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
1 & a_m & a_m^2 & \cdots & a_m^n \\
\end{bmatrix}

增广矩阵
\left[ \begin{array} {c c | c}
1 & 2 & 3 \\
\hline
4 & 5 & 6 \\
\end{array} \right]

表格
\begin{array} {c | lcr }
n & \text{Left} & \text{Center} & \text{Right} \\
\hline
1 & 0.02 & 1 & 123 \\
2 & -1 & 999 & -8 \\
3 & -20 & 1000 & 1+3i \\
\end{array}

方程组
\left \{
\begin{array}{c}
a_1x+b_1y = c_1 \\
a_2x+b_2y = c_2 \\
\end{array}
\right.

分段函数
y=\begin{cases}
2x+1, & x \leq0\\
x, & x>0
\end{cases}

f(n) = \begin{cases}
\cfrac n2, & if\ n\ is\ even \\
3n + 1, & if\ n\ is\ odd \\[3ex]
a + b, & if\ n\ =\ 0 \\
\end{cases}

连分式
x = a_0 +
    \frac {1^2} {a_1 +
    \frac {2^2} {a_2 +
    \frac {3^2} {a_3 + ... }}}

```
