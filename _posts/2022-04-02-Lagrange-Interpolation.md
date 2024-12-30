---
layout: post
title: Lagrange Interpolation
---

I sometimes need functions, both weird and sexy. 
The ones we can harcode tend to be too simple.

A fun solution is one where we define a set of points $\left\\{p_0, p_1, \ldots,  p_n\right\\}$, and ask ourselves: can we find some function that includes all these points in its image?

And in fact, we can! Given $n + 1$ points, there exists a **unique** polynomial of degree $n$ that meets this condition. Let's see how to find that polynomial.

A polynomial of degree $n$ is a function $P(x)$ that can be written as

$$P(x) = \sum_{k = 0}^{n} a_k x^k = a_0 + a_1 x + a_2 x^2 + \ldots + a_n x^n$$

The set $\left\\{a_0, a_1, \ldots, a_n\right\\}$ are the coefficients of the polynomium. 
As the values of these coefficients change, we obtain a different polynomial each time.
If we plot $P(x)$, the exact shape of the curve we obtain depends on the coefficients of the polynomium.

Our problem, informally speaking, is finding the coefficients' values for which $P(x)$ indeed traverses the requested $n +1$ points.

And what can we do to find them? Well, the points need to be in the image of $P(x)$. 
This means that for each point $p_j = (x_j, y_j)$ in the set, we know that $p(x_j) = y_j$ must hold.
As we have $n + 1$ points, we can create a system of $n + 1$ equations.

$$
\begin{align}
P(x_0) &= a_0 + a_1 x_0 + a_2 x^2_0 + \ldots + a_n x^n_0 = y_0 \\
P(x_1) &= a_0 + a_1 x_1 + a_2 x^2_1 + \ldots + a_n x^n_1 = y_1\\
&\;\;\vdots \notag \\
P(x_n) &= a_0 + a_1 x_n + a_2 x^2_n + \ldots + a_n x^n_n = y_n\\
\end{align}
$$

To find the answer to our problem, we need to solve the system. 
Things look good a priori, as in general we can only find the values of $n + 1$ unknowns (the coefficients) if we have the same number of equations.

We switch to matrix notation and define

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

Written this way, we can clearly see that the solution is

$$\mathbf{a} = \mathbf{X}^{-1} \cdot \mathbf{y}$$

End of story? Well...this is just starting, so maybe grub a cup of coffee and hold on to your belts!  

First, we should ask ourselves whether $\mathbf{X}^{-1}$ even exists. 

It turns out that $\mathbf{X}$ is not just any matrix, it is actually known as the the Vandermonde matrix.
There exists an inverser for this matrix as long as $\forall j \neq k,\; x_j \neq x_k$.
For this to be true, there should not be two points requesting different values for the same domain value.
Indeed, for a function to be well-defined, there should be a unique image per domain element. 
In addition, the $n + 1$ points given should be distinct.
If say we repeated one, we would only have $n$ distinct equations.
In this case, rather than a unique polynomial, the solution would consist of a sub-space of polynomials.

Second, to compute $\mathbf{X}^{-1}$, how much computational power do we need?
Without proof but without doubts, I'm sorry to break it to you, me and everyone else, but computing this matrix is an $O(n^3)$ operation...so as soon as $n$ becomes moderate, good luck with that! Fortunately, Lagrange came to the rescue and saved us all.

Lagrange changed a little bit the way we were thinking about the problem, and proposed to think the polynomial $P(x)$ as the sum of $n + 1$ functions

$$P(x) = \sum_{i=0}^{n} \alpha_i \phi_i(x)$$

and then proved that the solution was to define

$$\alpha_i = y_i$$

$$\phi_i(x) = \frac{\prod_{j \neq i}x - x_j}{\prod_{j \neq i}x_i - x_j}$$

Analyzing the expressions, we see that $\forall j \neq i, \, \phi_i(x_j) = 0$.
In addition, $\phi_i(x_i) = 1$ and as $alpha_i = y_i$, then $\alpha_i \phi_i(x) = y_i$.
This way, for each point $p_k$, there is only $\alpha_k \phi_k(x)$ composing $P(x)$ that evaluated at $x_k$ returns $y_k$, while the other $n$ functions return nil.
I mean...what a beast, such a clever and elegant solution. 
Chapeau! 

A natural question that follows is whether we could not have found a more "compact" solution that the one that Lagrange proposed. 
For example, we could try to look for some linear dependency among the $\phi_i(x)$ functions, remove it, and come up with a solution with less than $n + 1$ functions composing $P(x)$.
Well, dreaming is ok, but if that is how we come up with solutions, Lagrange clearly also dreamt, and he did it before us: his solution actually conforms a base of the $n + 1$ polynomium space. We are more used to the canonical base of this space, which is composed of functions $\left\\{1, t, t^2 \ldots, t^n\right\\}$. The canonical base looks simple and harmless, are we sure that the very clever formulation of Lagrange is also a base? Well, let's prove it to convince ourselves.

We can prove that Lagrange's solution conforms a base showing that the only way in which we the linear combination of the functions in his solution evaluate to zero for all $x$ is if and only if $\forall i, \alpha_i = 0$. If this must hold for any $x$, then in particular it must hold for all of the x-coordinates of the points that need to be traversed by $P(x)$, i.e., for the elements in $\left\\{x_0, x_1, \ldots, x_n\right\\}$ for which $P(x)$. Without loosing generality, taking $x_0$ as an example, we can compute: 

$$P(x_0) = \alpha_0 \phi_0(x_0) + \alpha_1 \phi_1(x_0) + \ldots + \alpha_n \phi_n(x_0)$$

but based on our previous analysis, we now that this ends up reducing to

$$P(x_0) = \alpha_0$$

Since $P(x_0) = 0$, then $\alpha_0 = 0$. If you are still hesitating, I invite you to replicate this analysis for the remaining $n$ values. At this point, voila, we have provedd that Lagrange's solution is indeed a base of the $n + 1$ polynomium space. His solution is simply beautiful, isn't it?

Now that we know many things about Lagrange's interpolation, the last point I want to address is how to implement it efficiently in code. To reason about this, let's first write down the final expression of $P(x)$:

$$P(x) = \sum_{i=0}^n y_i \frac{\prod_{j \neq i}x - x_j}{\prod_{j \neq i}x_i - x_j}$$

And I will continue writing soon...
