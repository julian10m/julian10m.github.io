---
layout: post
title: Lagrange Interpolation
---

It all starts with a simple question: given $n + 1$ points $\left\\{p_0, p_1, p_2, \ldots, p_j, \ldots,  p_n\right\\}$ where $p_j = (x_j, y_j)$, how can we find the **unique** polnoymium of degree $n$ that traverses such points?

Let's begin easy: "polnoymium of degree $n$" means that we are looking for a function $p(x)$ of the type:

$$p(x) = a_0 + a_1 x + a_2 x^2 + \ldots + a_n x^n$$

In particular, the objective is to find the values of the coefficients $\left\\{a_0, a_1, \ldots, a_n\right\\}$ for which $p(x)$ traverses the $n +1$ points that were given.

Hmm...we know that the $n + 1$ points are traversed by $p(x)$, hence we can turn this information into $n + 1$ equations. This is exactly what we need to when we need to univoquely determine $n + 1$ coefficients. The problem then becomes solving the system of equations:

$$
\begin{align}
p(x_0) &= a_0 + a_1 x_0 + a_2 x^2_0 + \ldots + a_n x^n_0 = y_0 \\
p(x_1) &= a_0 + a_1 x_1 + a_2 x^2_1 + \ldots + a_n x^n_1 = y_1\\
&\;\;\vdots \notag \\
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

End of story? Well, not really! Finding $\mathbf{X}^{-1}$ is an $O(n^3)$ operation, so good luck with that as soon as $n$ increases. Fortunately, Lagrange came up with a very neat solution to this problem.

Lagrange proposed that $p(x)$ could be written as the sum of $n + 1$ functions, i.e., $p(x) = \sum_{i=1}^n \alpha_i \phi_i(x)$, and then actually found their expressions: the coefficients had to be $\alpha_i = y_i$ and $\phi_i(x) = \frac{\prod_{j \neq i}x - x_j}{\prod_{j \neq i}x_i - x_j}$.

His idea was very clever because for any $\phi_i(x)$, it holds both that $\phi_i(x_i) = y_i$ and that $\forall j \neq i, \, \phi_i(x_j) = 0$. This means that at the x-coordinates of any of the $n + 1$ points that $p(x)$ had to cross, one of the functions composing $p(x)$ evaluated exactly to the y-coordinate of the point in question, and the other $n$ where null.

$$p(x) = \sum_{i=1}^n y_i \frac{\prod_{j \neq i}x - x_j}{\prod_{j \neq i}x_i - x_j}$$
