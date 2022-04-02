---
layout: post
title: Lagrange Interpolation
---

It all starts with a simple question: given $n + 1$ points $\left\\{p_0, p_1, p_2, \ldots, p_j, \ldots,  p_n\right\\}$ where $p_j = (x_j, y_j)$, how can we find the **unique** polnoymium of degree $n$ that traverses such points?

Let's begin easy: "polnoymium of degree $n$" means that we are looking for a function $p(x)$ of the type:

$$p(x) = a_0 + a_1 x + a_2 x^2 + \ldots + a_n x^n$$

In particular, the objective is to find the values of the coefficients $\left\\{a_0, a_1, \ldots, a_n\right\\}$ for which $p(x)$ traverses the $n +1$ points that were given.

Well, we have $n + 1$ points which chan be turned into $n + 1$ equations, so then we can find the $n + 1$ coefficients solving the system of equations:

$$
\begin{align}
p(x_0) &= a_0 + a_1 x_0 + a_2 x^2_0 + \ldots + a_n x^n_0 = y_0 \\
p(x_1) &= a_0 + a_1 x_1 + a_2 x^2_1 + \ldots + a_n x^n_1 = y_1\\
&\vdotswithin{=}  \notag \\
p(x_n) &= a_0 + a_1 x_n + a_2 x^2_n + \ldots + a_n x^n_n = y_n\\
\end{align}
$$

which we can write as $\mathbf{X} \cdot \mathbf{a} = \mathbf{y}$ using matrix notation, where:

$$
\mathbf{X} = \begin{bmatrix}
1 & x_0 & \cdots & x^n_0 \\
1 & x_1 & \cdots & x^n_1 \\
\vdots & \vdots & & \vdots \\
1 & x_n & \cdots & x^n_n \\
\end{bmatrix}\quad
\mathbf{a}  = \begin{bmatrix}
a_0 \\
a_1 \\
\vdots \\
a_n \\
\end{bmatrix}\quad
\mathbf{y}  = \begin{bmatrix}
y_0 \\
y_1 \\
\vdots \\
y_n \\
\end{bmatrix}
$$
