---
layout: post
title: Playing with rectangles and Lua2D
publish: true
---

### Cartesian and polar coordinates

A point in a plane can be described by its coordinates with respect to the origin, e.g. $p = (x, y)$. 

But how do $x$ and $y$ change if we rotate the point with respect to the origin? 

Well, first we need to note that in those cases what prevails is the distance $r$ between the origin and the point such that $r = \sqrt{x^2 + y^2}$. We can also keep track of the rotation angle $\theta$, which we can measure between $p$ and the x-axis. As we rotate the point, we can see that the trajectory that $p$ follows is that of the circumference of a circle with radius $r$.

Ok, but we wanted to know the values of $x$ and $y$ for any rotation angle! Sure thing, this is where geometry comes in place. Indistinctly of the exact value of $\theta$, we should note that $x$, $y$ and $r$ actually form an orthogonal rectangle. In this triangle, the 90 degrees angle always  appears between $x$ and $y$. According to trigonometry, we know that it must then hold that:

$$
\begin{align}
x &= r * \cos(\theta) \\
y &= r * \sin(\theta) \\
\end{align}
$$

Hence, as we can see, we can characterize a point by either its coordinates $x$ and $y$, or by its radius $r$ and rotation angle $\theta$.

### Centered rectangles

A rectangle if formed by the intersection of 2 pairs of parallel lines forming a 90 degree angle between themselves. The formed rectangle ends up having a width $w$ and some height $h$, and has its 4 corners located in the places where the lines intersect. If we center the rectangle on the origin, then the position of its corners can be described as

$$
\begin{align}
p1 &= \left(\frac{w}{2} ; \frac{h}{2}\right) \\
p2 &= \left(-\frac{w}{2} ; \frac{h}{2}\right) \\
p3 &= \left(-\frac{w}{2} ; -\frac{h}{2}\right) \\
p4 &= \left(\frac{w}{2}  ; -\frac{h}{2}\right)
\end{align}
$$

Knowing the corners of the rectangle is useful because they help us delimit the rectangle. While this is true, we should not forget that if we look at them separately, they are merely points in a plane. As such, we can use what we learned before to describe their position, i.e., use polar coordinates instead of cartesian. For this, we simply need to describe their radius $r$ and rotation angle. 

By simple inspecttion, we can see that $r$ must equal half of the diagonal of the rectangle, i.e. $r = \frac{1}{2} \sqrt{(w^2 + h^2)}$. On the other hand, we can see that for the 4 points, there is a fixed angle $\alpha = \arctan(\frac{y}{x})$ that can be used to describe the angle of each point. Taking this into account, we can describe the position of the corners of the rectangle as 

$$
\begin{align}
p1 &= \left(r * cos(\alpha); r * \sin(\alpha)\right) \\
p2 &= \left(r * \cos(\pi - \alpha); r * \sin(\pi - \alpha)\right) \\
p3 &= \left(r * \cos(\pi + \alpha); r * \sin(\pi + \alpha)\right) \\
p4 &= \left(r * \cos(-\alpha); r * \sin(-\alpha)\right)
\end{align}
$$

### Moving rectangles: rotation and displacement

How does everything change if the rectangle is no longer static? What if besides displacing, the rengtangle is also rotating? Sounds tough, right? But hey, don't worry...to solve this we can rely on the superposition theorem and analyse each of the effects separately.

Let's first focus on the displacement speed, since it might be the easiest to understand. If our rectangle moves in any direction with any given velocity, as long as we are able to express how the position changes over time, we can easily incorporate this in our formulas. Let $x(t)$ and $y(t)$ denote the position of the center of the rectangle at each moment, then we can write

<!---
$$
\begin{align}
p1 &= \left(x(t) + r * cos(\alpha); y(t) + r * \sin(\alpha)\right)\\
p2 &= \left(x(t) + r * \cos(\pi - \alpha); y(t) + r * \sin(\pi - \alpha)\right) \\
p3 &= \left(x(t) + r * \cos(\pi + \alpha); y(t) + r * \sin(\pi + \alpha)\right) \\
p4 &= \left(x(t) + r * \cos(-\alpha); y(t) + r * \sin(-\alpha)\right)
\end{align}
$$
-->

| Point | x-coordinate                 | y-coordinate                 |
|-------|-----------------------------|-----------------------------|
| p1    | $x(t) + r \cos(\alpha)$     | $y(t) + r \sin(\alpha)$     |
| p2    | $x(t) + r \cos(\pi - \alpha)$ | $y(t) + r \sin(\pi - \alpha)$ |
| p3    | $x(t) + r \cos(\pi + \alpha)$ | $y(t) + r \sin(\pi + \alpha)$ |
| p4    | $x(t) + r \cos(-\alpha)$    | $y(t) + r \sin(-\alpha)$    |

with a velocity $\mathbf{v(t)} = (v_x(t), v_y(t))

follows a uniform rectilinear motion, i.e., moves with a constant velocity $\mathbf{v} = (v_x, v_y)$

Well, thanks to our polar coordinates, it is actually not that hard! We can update our previous formula as:


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
