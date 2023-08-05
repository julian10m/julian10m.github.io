---
layout: post
title: Playing with rectangles and Lua2D
publish: true
---

### Cartesian and polar coordinates

A point in a plane can be described by its relative position with respect to the origin, e.g. $p = (x, y)$. These are known as the **cartesian coordinates**.

But if we rotate the point with respect to the origin, how do $x$ and $y$ change? 

Well, let's think about it. If the point is in $(x, y)$, then Pitagoras says that it must be at a distance $r = \sqrt{x^2 + y^2}$ from the origin. Now, does the distance change just for rotating the point? It could, but if we assume we rotate the point with respect to the origin, then $r$ prevails unchanged. Hence, the only thing we need to keep track of is the rotation angle $\theta$, which we can measure between $p$ and the x-axis. As we rotate the point, we can see that $p$ always stands in the circumference of a circle with radius $r$. The pair $(r, \theta)$ represents the **polar coordinates** of the point.

Ok, but we wanted to know the values of $x$ and $y$ for any rotation angle! No problem, this is where geometry comes to the rescue. Indistinctly of the exact value of $\theta$, we should note that we can always form an orthogonal rectangle: $x$ and $y$ being the cathetus or legs, and $r$ the hypotenuse. In this triangle, a 90 degrees angle always appears between $x$ and $y$. According to the trigonometry laws, we know that it must then hold that:

$$
\begin{align}
x &= r \cdot \cos(\theta) \\
y &= r \cdot \sin(\theta) \\
\end{align}
$$

Hence, as we can see, we can characterize a point by either its coordinates $x$ and $y$, or by its radius $r$ and rotation angle $\theta$. For completeness, if $x$ and $y$ were known, we could calculate the rotation angle as $\theta = \arctan(\frac{y}{x})$.

### Centered rectangles

A rectangle if formed by the intersection of 2 pairs of parallel lines forming a 90 degree angle between themselves. The formed rectangle ends up having a width $w$ and some height $h$, and has its 4 corners located in the places where the lines intersect. If we center the rectangle on the origin, then the position of its corners can be described as

$$
\begin{align}
p_1 &= \left(\frac{w}{2} ; \frac{h}{2}\right) \\
p_2 &= \left(-\frac{w}{2} ; \frac{h}{2}\right) \\
p_3 &= \left(-\frac{w}{2} ; -\frac{h}{2}\right) \\
p_4 &= \left(\frac{w}{2}  ; -\frac{h}{2}\right)
\end{align}
$$

Knowing the corners of the rectangle is useful because they help us delimit the rectangle. While this is true, we should not forget that if we look at them separately, they are merely points in a plane. As such, we can use what we learned before to describe their position, i.e., use polar coordinates instead of cartesian. For this, we simply need to describe their radius $r$ and rotation angle. 

By simple inspecttion, we can see that $r$ must equal half of the diagonal of the rectangle, i.e. $r = \frac{1}{2} \sqrt{(w^2 + h^2)}$. On the other hand, we can see that for the 4 points, there is a fixed angle $\alpha = \arctan(\frac{y}{x})$ that can be used to describe the angle of each point. Taking this into account, we can describe the position of the corners of the rectangle as 

<!---
$$
\begin{align}
p_1 &= \left(r \cdot cos(\alpha); r \cdot \sin(\alpha)\right) \\
p_2 &= \left(r \cdot \cos(\pi - \alpha); r \cdot \sin(\pi - \alpha)\right) \\
p_3 &= \left(r \cdot \cos(\pi + \alpha); r \cdot \sin(\pi + \alpha)\right) \\
p_4 &= \left(r \cdot \cos(-\alpha); r \cdot \sin(-\alpha)\right)
\end{align}
$$
-->

| Point | x-coordinate                 | y-coordinate                 |
|:-------:|:-----------------------------:|:-----------------------------:|
| $p_1$    | $r \cdot \cos(\alpha)$     | $r \cdot \sin(\alpha)$     |
| $p_2$    | $r \cdot \cos(\pi - \alpha)$ | $r \cdot \sin(\pi - \alpha)$ |
| $p_3$    | $r \cdot \cos(\pi + \alpha)$ | $r \cdot \sin(\pi + \alpha)$ |
| $p_4$    | $r \cdot \cos(-\alpha)$    | $r \cdot \sin(-\alpha)$    |

PS: I love math notation, but it was becoming quite dense to read, so I switched to the use of tables :) 

### Moving rectangles: rotation and displacement

How does everything change if the rectangle is no longer static? What if besides displacing, the rengtangle is also rotating? Sounds tough, right? But hey, don't worry...step by step!

To solve this we can rely on the superposition theorem and analyse each of the effects separately. Let's first focus on the displacement speed, since it might be the easiest to understand. If our rectangle moves in any direction with any given velocity, as long as we are able to express how the position changes over time, we can easily incorporate this in our formulas. Since the relative position of the corners with respect to the origin does not change, then their position at any moment must be that as if the rectangle was centered, plus the shift we have made to the center. On the other hand, if the rectangle is centered and only rotates, then the relative distance to the center is fixed for each point; the only thing that varies is the relative angle for each point.

Let $x(t)$ and $y(t)$ denote the position of the center of the rectangle at each moment, and $\beta(t)$ be the rotation angle in time. Taking advantage of the polar coordinates, it is actually not that hard to incorporate both effects on our original formula

<!---
$$
\begin{align}
p_1 &= \left(x(t) + r \cdot cos(\alpha); y(t) + r \cdot \sin(\alpha)\right)\\
p_2 &= \left(x(t) + r \cdot \cos(\pi - \alpha); y(t) + r \cdot \sin(\pi - \alpha)\right) \\
p_3 &= \left(x(t) + r \cdot \cos(\pi + \alpha); y(t) + r \cdot \sin(\pi + \alpha)\right) \\
p_4 &= \left(x(t) + r \cdot \cos(-\alpha); y(t) + r \cdot \sin(-\alpha)\right)
\end{align}
$$
-->

| Point | x-coordinate                 | y-coordinate                 |
|:-------:|:-----------------------------:|:-----------------------------:|
| $p_1$     | $x(t) + r \cdot \cos\left(\beta(t) + \alpha\right)$     | $y(t) + r \cdot \sin\left(\beta(t) + \alpha\right)$     |
| $p_2$     | $x(t) + r \cdot \cos\left(\pi + \beta(t) - \alpha\right)$ | $y(t) + r \cdot \sin\left(\pi + \beta(t) - \alpha\right)$ |
| $p_3$     | $x(t) + r \cdot \cos\left(\pi + \beta(t) + \alpha\right)$ | $y(t) + r \cdot \sin\left(\pi + \beta(t) + \alpha\right)$ |
| $p_4$     | $x(t) + r \cdot \cos\left(\beta(t) -\alpha\right)$    | $y(t) + r \cdot \sin\left(\beta(t) -\alpha\right)$    |

While the formulas in the table are true in general, maybe they feel too abstract. Let's analyze a practical scenario.

### Case of study: URM + UCM 

To better understand our result, we can focus on the case of the combination of a uniform rectilinear motion (URM) with a uniform circular motion (UCM). This is in fact what I ended up implementing, thus from here the special interest.

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
