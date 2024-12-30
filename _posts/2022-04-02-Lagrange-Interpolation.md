---
layout: post
title: Lagrange Interpolation
---

I sometimes need functions, both weird and sexy. 
The ones we can harcode tend to be too simple.

A fun solution is one where we define a set of points $\left\\{p_0, p_1, \ldots, p_j, \ldots,  p_n\right\\}$, and ask ourselves: can we find some function that includes all these points in its image?

And in fact, we can! Given $n + 1$ points, there exists a **unique** polnoymium of degree $n$ that meets this condition. Let's see how to find that polnoymium.

A "polnoymium of degree $n$", is a function $p(x)$ that can be written as

$$p(x) = a_0 + a_1 x + a_2 x^2 + \ldots + a_n x^n$$

The set $\left\\{a_0, a_1, \ldots, a_n\right\\}$ are the coefficients of the polynomium. 
As the values of these coefficients change, we obtain a different polnoymium each time.
If we plot $p(x)$, the exact shape of the curve we obtain depends on the coefficients of the polynomium.

Our problem, informally speaking, is finding the coefficients' values for which $p(x)$ indeed traverses the requested $n +1$ points.

And what can we do to find them? Well, the points need to be in the image of $p(x)$. 
This means that for each point $p_j = (x_j, y_j)$ in the set, we know that $p(x_j) = y_j$ must hold.
As we have $n + 1$ points, we can create a system of $n + 1$ equations.

$$
\begin{align}
p(x_0) &= a_0 + a_1 x_0 + a_2 x^2_0 + \ldots + a_n x^n_0 = y_0 \\
p(x_1) &= a_0 + a_1 x_1 + a_2 x^2_1 + \ldots + a_n x^n_1 = y_1\\
&\;\;\vdots \notag \\
p(x_n) &= a_0 + a_1 x_n + a_2 x^2_n + \ldots + a_n x^n_n = y_n\\
\end{align}
$$

To find the answer to our problem, we need to solve the system. 
Things look good a priori, as in general we can only find the values of $n + 1$ unknowns (the coefficients) if we have the same number of equations.
We switch to matrix notation and define:

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

so that then we can describe our problem more compactly as

$$\mathbf{X} \cdot \mathbf{a} = \mathbf{y}$$

Hence, we can find a unique solution by simply finding

$$\mathbf{a} = \mathbf{X}^{-1} \cdot \mathbf{y}$$

End of story? Well, not really! 

First, we should ask ourselves whether $\mathbf{X}^{-1}$ even exists. 
It turns out that $\mathbf{X}$ is known as the the Vandermonde matrix.
We can indeed find an inverse for this matrix as long as $\forall j \neq k \;,\; x_j \ x_k$.
In words, as long as the points we are given are not weirdly defined, everything is ok.
For this to be true, there should not be two points requesting different values for the same domain value, as for a function to be well-defined, there should be a unique image per domain element. 
In addition, the set should not include the same point twice. 
In theory, this is ensured by the fact that we have a set of points, but in practice we might use a list and fail to notice the mistake.
If this happened, we would only have $n$ distinct equations, leaving space for a sub-space of polnoymiums complying with the constraints rather than a unique solution.

Second, how hard is it to compute $\mathbf{X}^{-1}$?
Well, I'm sorry to break it to you, me and everyone else, but computing this matrix is an $O(n^3)$ operation...so as soon as $n$ becomes moderate, good luck with that! Fortunately, Lagrange came to the rescue and saved us all.

Lagrange changed a little bit the way we were thinking about the problem, and proposed to think the polnoymium $p(x)$ as the sum of $n + 1$ functions, i.e., 

$$p(x) = \sum_{i=0}^{n} \alpha_i \phi_i(x)$$

and then proved that  

$$\alpha_i = y_i$$

$$\phi_i(x) = \frac{\prod_{j \neq i}x - x_j}{\prod_{j \neq i}x_i - x_j}$$

Analyzing the expressions, for any $\phi_i(x)$, it holds that $\forall j \neq i, \, \phi_i(x_j) = 0$.
In addition, $\phi_i(x_i) = 1$ and hence $\alpha_i \phi_i(x) = y_i$.
This way, for each point $x_k$, there is only $\alpha_k \phi_k(x)$ composing $p(x)$ that evaluated at $x_k$ returns $y_k$, while the other $n$ functions return nil.
I mean...what a beast, such a clever and elegant solution. 
Chapeau! 

A natural question that follows is whether we could not have found a more "compact" solution that the one that Lagrange proposed. 
For example, we could try to look for some linear dependency among the $\phi_i(x)$ functions, remove it, and come up with a solution with less than $n + 1$ functions composing $p(x)$.
Well, dreaming is ok, but if that is how we come up with solutions, Lagrange clearly also dreamt, and he did it before us: his solution actually conforms a base of the $n + 1$ polynomium space. We are more used to the canonical base of this space, which is composed of functions $\left\\{1, t, t^2 \ldots, t^n\right\\}$. The canonical base looks simple and harmless, are we sure that the very clever formulation of Lagrange is also a base? Well, let's prove it to convince ourselves.

We can prove that Lagrange's solution conforms a base showing that the only way in which we the linear combination of the functions in his solution evaluate to zero for all $x$ is if and only if $\forall i, \alpha_i = 0$. If this must hold for any $x$, then in particular it must hold for all of the x-coordinates of the points that need to be traversed by $p(x)$, i.e., for the elements in $\left\\{x_0, x_1, \ldots, x_n\right\\}$ for which $p(x)$. Without loosing generality, taking $x_0$ as an example, we can compute: 

$$p(x_0) = \alpha_0 \phi_0(x_0) + \alpha_1 \phi_1(x_0) + \ldots + \alpha_n \phi_n(x_0)$$

but based on our previous analysis, we now that this ends up reducing to

$$p(x_0) = \alpha_0$$

Since $p(x_0) = 0$, then $\alpha_0 = 0$. If you are still hesitating, I invite you to replicate this analysis for the remaining $n$ values. At this point, voila, we have provedd that Lagrange's solution is indeed a base of the $n + 1$ polynomium space. His solution is simply beautiful, isn't it?

Now that we know many things about Lagrange's interpolation, the last point I want to address is how to implement it efficiently in code. To reason about this, let's first write down the final expression of $p(x)$:

$$p(x) = \sum_{i=0}^n y_i \frac{\prod_{j \neq i}x - x_j}{\prod_{j \neq i}x_i - x_j}$$

And I will continue writing soon...
