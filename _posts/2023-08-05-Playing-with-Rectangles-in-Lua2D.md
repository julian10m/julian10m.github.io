---
layout: post
title: Playing with rectangles and Lua2D
publish: true
---

### Cartesian and polar coordinates

We usually use cartesian coordinates to individualize points. For instance, when we write $p = (x, y)$, we express that originating from the origin $(0, 0)$, then $x$ and $y$ are the distances to be traversed along the x-axis and y-axis, respectively, to arrive at $p$. However, this is not the only option: we can also user polar coordinates, and write $p = (r, \theta)$. In this notation, we have both a radial and angular coordinate: $r$ is the distance between $p$ and the origin, and $\theta$ is the rotation angle of $p$ with respect to the x-axis. 

In practice, it is actually quite convenient to have both systems, since some problems are easier to express in cartesian coordinates, and other in polar coordinates. For example, if we rotate the point $p$ with respect to the origin by $\Delta$ degrees, we can trivially deduct that $r$ remains constant, and that only $\theta$ now becomes $\theta + \Delta$. As we vary $\Delta$, it is easy to see that $p$ moves along the circumference of a circle with radius $r$. While $(r, \theta + \Delta)$ fully describes the new position of $p$, one can still wonder how $x$ and $y$ changed, and for that we have conversion forumulas.

To convert from polar to cartesian coordinates, we first need to note that the radial and the cartesian coordinates of any point form an orthogonal triangle. In the triangle that is formed for $p$, $x$ and $y$ are the cathetus or legs, and $r$ is the hypotenuse. In addition, the angle between $x$ and $r$ is exactly $\theta$. According to the trigonometry laws, we can then write:

$$
\begin{align}
x &= r \cdot \cos(\theta) \\
y &= r \cdot \sin(\theta)
\end{align}
$$

If instead we knew $x$ and $y$, and would interested in finding $r$ and $\theta$, we can use the following formulas that can be derived with some basic algebra:

$$
\begin{align}
r &= \sqrt{x^2 + y^2} \\
\theta &= \arctan(\frac{y}{x})
\end{align}
$$

These formulas not only allow to pass from one notation to the other, but also allow to express each system as a function of the other. In what follows, we are particularly interested on viewing expressing cartesian coordinates as a function of the polar ones.



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

An interesting case of study is when we the rectangle follows both a uniform rectilinear motion (URM) and a uniform circular motion (UCM). At this point, I must admit that calling this "an interesting case" might be a biased opinion: this scenerario is particularly appeling for me because it is the one I implemented in my code.

With an URM, the rectangle moves with a constant velocity $v_x$ and $v_y$ on the x-axis and y-axis, respectively. If in addition $x_0$ and $y_0$ represent the initial position of the center of the rectangle -- which could then be different than the origin -- we can then write:

$$
\begin{align}
x(t) &= v_x \cdot t + x_0\\
y(t) &= v_y \cdot t + y_0
\end{align}
$$

On the other hand, for an UCM, we have a rotation velocity that has a constant modulus $w$. Considering an initial rotation angle of the rectangle, denoted $\beta_0$, we have:

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
