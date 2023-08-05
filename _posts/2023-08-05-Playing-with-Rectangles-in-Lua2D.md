---
layout: post
title: Playing with rectangles and Lua2D
publish: true
---

### Cartesian and polar coordinates

A point $p$ can be described by its cartesian coordinates, e.g., in 2D we may have $p = (x, y)$ where $x$ and $y$ represent the value on the x-axis and y-axis respectively. However, this is not the only option: we can also describe $p$ by its polar coordinates $(r, \theta)$, where $r$ is the distance between $p$ and the origin, and $\theta$ is the rotation angle of that point with respect to the x-axis. In this case $r$ is the measure on the radial coordinate and $\theta$ is the counterpart for the angular coordinate.

To convert from polar coordinates to cartesian coordinates, the trick is to note that the radial coordinate and the cartesian coordinates of $p$ for an orthogonal triangle. In this triangle, $x$ and $y$ are the cathetus or legs, and $r$ is the hypotenuse. In addition, the angle between $x$ and $r$ is exactly $\theta$. According to the trigonometry laws, then we can write:

$$
\begin{align}
x &= r \cdot \cos(\theta) \\
y &= r \cdot \sin(\theta)
\end{align}
$$

If instead we are interested in the conversion on the opposite direction, applying some simple algebra we can prove that:

$$
\begin{align}
r &= \sqrt{x^2 + y^2} \\
\theta &= \arctan(\frac{y}{x})
\end{align}
$$

In practice, it is actually quite convenient to have both systems, since some problems are easier to express in cartesian coordinates, and other in polar coordinates. For example, if we rotate the point $p$ with respect to the origin, we can deduct that $r$ actually remains constant, and that only $\theta$ varies. Indeed, as we rotate the point, $p$ moves along the circumference of a circle with radius $r$. While this analysis is enough to completely describe the new position of $p$, if we need to, we can do the math and find the new values of $x$ and $y$ relying on the conversions previously introduced.

### Centered rectangles

A rectangle is formed by the intersection of 2 pairs of parallel lines forming an angle of 90 degrees between each pair. The corners of the rectangle are located where the lines intersect. If we center the rectangle on the origin, and consider the rectangle has a width $w$ and a height $h$, we can write the position of its corners as:

$$
\begin{align}
p_1 &= \left(\frac{w}{2} ; \frac{h}{2}\right) \\
p_2 &= \left(-\frac{w}{2} ; \frac{h}{2}\right) \\
p_3 &= \left(-\frac{w}{2} ; -\frac{h}{2}\right) \\
p_4 &= \left(\frac{w}{2}  ; -\frac{h}{2}\right)
\end{align}
$$

While this is ok as it is, as we saw before, we can also write the cartesian coordinates as a function of the polar coordinates. By simple symmetry, we can see that all corners are located at the same distance $r$ from the origin. Moreover, $r$ is half of the diagonal of the rectangle, i.e. $r = \frac{1}{2} \sqrt{(w^2 + h^2)}$. On the other hand, we can see that for the 4 points, there is a fixed angle $\alpha = \arctan(\frac{y}{x})$ that can be used to describe the angle at which we can them. Taking this into account, and abandoning the math notation to ease readability, the next table describes another way in which we can describe the position of the corners of the rectangle:

| Point | x-coordinate                 | y-coordinate                 |
|:-------:|:-----------------------------:|:-----------------------------:|
| $p_1$    | $r \cdot \cos(\alpha)$     | $r \cdot \sin(\alpha)$     |
| $p_2$    | $r \cdot \cos(\pi - \alpha)$ | $r \cdot \sin(\pi - \alpha)$ |
| $p_3$    | $r \cdot \cos(\pi + \alpha)$ | $r \cdot \sin(\pi + \alpha)$ |
| $p_4$    | $r \cdot \cos(-\alpha)$    | $r \cdot \sin(-\alpha)$    |
  
### Moving rectangles: rotation and displacement

In this section, we are interested in analyzing the position of non-static rectangles over time. Indeed, if the rectangle moves, how should we update the previous table to account for that? Furthermore, what happens if besides displacing, the rengtangle is also rotating? 

Focusing on the displacement, it is simple to see that the corners move as much as the center does, i.e., the relative position of the corners with respect to the origin does not change. Let $x(t)$ and $y(t)$ denote the position of the center of the rectangle at each moment, then we just need to add these shifts to our formulas:

| Point | x-coordinate                 | y-coordinate                 |
|:-------:|:-----------------------------:|:-----------------------------:|
| $p_1$     | $x(t) + r \cdot \cos\left(\alpha\right)$     | $y(t) + r \cdot \sin\left(\alpha\right)$     |
| $p_2$     | $x(t) + r \cdot \cos\left(\pi - \alpha\right)$ | $y(t) + r \cdot \sin\left(\pi - \alpha\right)$ |
| $p_3$     | $x(t) + r \cdot \cos\left(\pi + \alpha\right)$ | $y(t) + r \cdot \sin\left(\pi + \alpha\right)$ |
| $p_4$     | $x(t) + r \cdot \cos\left(-\alpha\right)$    | $y(t) + r \cdot \sin\left(-\alpha\right)$    |


On the other hand, if the centered rectangle just rotates, the relative position of the corners with respect to the origin or its center changes. While the distance is fixed, the relative angle of each point actually changes. In fact, the relative angles change as much as the rotation angle of the rectangle over time, denoted $\beta(t)$, dictates:

| Point | x-coordinate                 | y-coordinate                 |
|:-------:|:-----------------------------:|:-----------------------------:|
| $p_1$     | $r \cdot \cos\left(\beta(t) + \alpha\right)$     | $r \cdot \sin\left(\beta(t) + \alpha\right)$     |
| $p_2$     | $r \cdot \cos\left(\beta(t) + \pi - \alpha\right)$ | $r \cdot \sin\left(\beta(t) + \pi - \alpha\right)$ |
| $p_3$     | $r \cdot \cos\left(\beta(t) + \pi + \alpha\right)$ | $r \cdot \sin\left(\beta(t) + \pi + \alpha\right)$ |
| $p_4$     | $r \cdot \cos\left(\beta(t) - \alpha\right)$    | $r \cdot \sin\left(\beta(t) -\alpha\right)$    |

Finally, to answer the questions, we can rely on the superposition theorem. According to this principle, the combined effect of the displacement and the rotation can be simply expressed as the sum of their separate effects, hence we can write:

| Point | x-coordinate                 | y-coordinate                 |
|:-------:|:-----------------------------:|:-----------------------------:|
| $p_1$     | $x(t) + r \cdot \cos\left(\beta(t) + \alpha\right)$     | $y(t) + r \cdot \sin\left(\beta(t) + \alpha\right)$     |
| $p_2$     | $x(t) + r \cdot \cos\left(\beta(t) + \pi - \alpha\right)$ | $y(t) + r \cdot \sin\left(\beta(t) + \pi - \alpha\right)$ |
| $p_3$     | $x(t) + r \cdot \cos\left(\beta(t) + \pi + \alpha\right)$ | $y(t) + r \cdot \sin\left(\beta(t) + \pi + \alpha\right)$ |
| $p_4$     | $x(t) + r \cdot \cos\left(\beta(t) - \alpha\right)$    | $y(t) + r \cdot \sin\left(\beta(t) -\alpha\right)$    |

### Case of study: URM + UCM 

An interesting case of study is when we the rectangles follows both a uniform rectilinear motion (URM) and a uniform circular motion (UCM). At this point, I must admit that calling this "an interesting case" might be a biased opinion: this scenerario is particularly appeling for me because it is the one I implementing in my code.

With an URM, the rectangles moves with a constant velocity $\mathbf{v} = (v_x, v_y)$. If in addition $x_0$ and $y_0$ represent the initial position (which could be different than the origin then) of the rectangle, we can then write the center position over time as 

$$
\begin{align}
x(t) &= v_x \cdot t + x_0\\
y(t) &= v_y \cdot t + y_0
\end{align}
$$

On the other hand, for an UCM, we have a rotation velocity that has a constant modulus $w$. Considering $\beta_0$ represents the initial rotation angle of the rectangle, we have

$$\beta(t) = w \cdot t + \beta_0$$

We could take the new formulas and replace them in the previous table for completeness. However we will not do it since in practice it is easier to separetely calculate $x(t)$, $y(t)$ and $\beta(t)$, and then plug their values into the formulas.

<!---
We can use these formulas on our previous table. Replacing, we have

| Point | x-coordinate                 | y-coordinate                 |
|:-------:|:-----------------------------:|:-----------------------------:|
| $p_1$     | $v_x \cdot t + r \cos(w \cdot t + \beta_0 + \alpha)$     | $v_y \cdot t + r \sin(w \cdot t + \beta_0 + \alpha)$     |
| $p_2$     | $v_x \cdot t + r \cos(\pi + w \cdot t + \beta_0 - \alpha)$ | $v_y \cdot t + r \sin(\pi + w \cdot t + \beta_0 - \alpha)$ |
| $p_3$     | $v_x \cdot t + r \cos(\pi + w \cdot t + \beta_0 + \alpha)$ | $v_y \cdot t + r \sin(\pi + w \cdot t + \beta_0 + \alpha)$ |
| $p_4$     | $v_x \cdot t + r \cos(w \cdot t + \beta_0 -\alpha)$    | $v_y \cdot t + r \sin(w \cdot t + \beta_0 -\alpha)$    |
-->




It all starts with a simple question: given $n + 1$ points $\left\\{p_0, p_1, \ldots, p_j, \ldots,  p_n\right\\}$ where $p_j = (x_j, y_j)$, how can we find the **unique** polnoymium of degree $n$ that traverses such points?

Let's begin easy: "polnoymium of degree $n$" means that we are looking for a function $p(x)$ of the type:

$$p(x) = a_0 + a_1 ( a_2 x^2 + \ldots + a_n x^n$$

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

Lagrange proposed that $p(x)$ could be written as the sum of $n + 1$ functions, i.e., 

$$p(x) = \sum_{i=1}^n \alpha_i \phi_i(x)$$

and proved that, to pass through the required $n + 1$ points, then

$$\alpha_i = y_i$$

$$\phi_i(x) = \frac{\prod_{j \neq i}x - x_j}{\prod_{j \neq i}x_i - x_j}$$

His idea was very clever because for any $\phi_i(x)$, it holds both that $\phi_i(x_i) = y_i$ and that $\forall j \neq i, \, \phi_i(x_j) = 0$. This means that at the x-coordinates of any of the $n + 1$ points that $p(x)$ had to cross, one of the functions composing $p(x)$ evaluated exactly to the y-coordinate of the point in question while the other $n$ where null.

A natural question that follows is whether we could not have found a more "compact" solution that the one that Lagrange proposed. For example, we could try to look for some linear dependency among the $\phi_i(x)$ functions, remove it, and come up with a solution with less than $n + 1$ functions composing $p(x)$. Well, dreaming is ok, but if that is how we come up with solutions, Lagrange clearly also dreamt, and he did it before us: his solution actually conforms a base of the $n + 1$ polynomium space. We are more used to the canonical base of this space, which is composed of functions $\left\\{1, t, t^2 \ldots, t^n\right\\}$. The canonical base looks simple and harmless, are we sure that the very clever formulation of Lagrange is also a base? Well, let's prove it to convince ourselves.

We can prove that Lagrange's solution conforms a base showing that the only way in which we the linear combination of the functions in his solution evaluate to zero for all $x$ is if and only if $\forall i, \alpha_i = 0$. If this must hold for any $x$, then in particular it must hold for all of the x-coordinates of the points that need to be traversed by $p(x)$, i.e., for the elements in $\left\\{x_0, x_1, \ldots, x_n\right\\}$ for which $p(x)$. Without loosing generality, taking $x_0$ as an example, we can compute: 

$$p(x_0) = \alpha_0 \phi_0(x_0) + \alpha_1 \phi_1(x_0) + \ldots + \alpha_n \phi_n(x_0)$$

but based on our previous analysis, we now that this ends up reducing to

$$p(x_0) = \alpha_0$$

Since $p(x_0) = 0$, then $\alpha_0 = 0$. If you are still hesitating, I invite you to replicate this analysis for the remaining $n$ values. At this point, voila, we have provedd that Lagrange's solution is indeed a base of the $n + 1$ polynomium space. His solution is simply beautiful, isn't it?

Now that we know many things about Lagrange's interpolation, the last point I want to address is how to implement it efficiently in code. To reason about this, let's first write down the final expression of $p(x)$:

$$p(x) = \sum_{i=0}^n y_i \frac{\prod_{j \neq i}x - x_j}{\prod_{j \neq i}x_i - x_j}$$

And I will continue writing soon...
