---
layout: post
title: Softmax Regression
---

Softmax regression is a supervised machine learning algorithm used in classification tasks where a target variable $y$ may take $K$ values, and we are interested in labeling an observation composed of $N$ features/predictors. While workarounds such as OvR and OvO allow to use binary classifiers (e.g. logistic regression) in multinomial classification tasks, softmax regression is a method specifically conceived for this purpose, i.e. cases where $K>2$. In particular, softmax regression jointly tunes $K$ weighting vectors $\\{\mathbf{w_1}, \mathbf{w_2}, \ldots, \mathbf{w_K}\\}$ and intercepts $\\{b_1, b_2, \ldots, b_K\\}$ that are used to estimate one a posteriori probability per class.

## Softmax Function

In softmax regression, the sigmoid function $\sigma(z)$ used in logistic regression is replaced with the softmax function, i.e. with a vector function $\Psi: \mathbb{R}^K \to (0, 1)^K$ defined as 

$$\Psi(z_1, z_2, \ldots, z_K) = \begin{bmatrix}
\psi_1(z_1, z_2, \ldots, z_K) \\
\psi_2(z_1, z_2, \ldots, z_K) \\
\vdots \\
\psi_K(z_1, z_2, \ldots, z_K) \\
\end{bmatrix}$$

where each $\psi_k:  \mathbb{R}^K \to (0, 1)$ is a scalar function 

$$\psi_k(z_1, z_2, \ldots, z_K) = \frac{e^{z_k}}{\sum_{l=1}^K e^{z_l}} \label{psi_component}$$ 

According to this definition, we can see that the denominator acts as a normalization factor, ensuring that $0 <  \psi_k(z_1, z_2, \ldots, z_K) < 1$ for all components. Moreover, we can see that $\sum_{k=1}^K \psi_k(z_1, z_2, \ldots, z_K) = 1$. These two characteristics of the softmax function allow to use it to express the probability distribution of $y$ given $\mathbf{x}$ across the $K$ classes. 


In addition, note that when $K=2$, evaluating for $z_1 = 0$ and $z_2= z$, we have

$$\Psi(0, z) = \begin{bmatrix}
\frac{1}{1 + e^z} \\[1.5ex]
\frac{e^z}{1 + e^z} 
\end{bmatrix} = 
\begin{bmatrix}
1 - \sigma(z) \\[1.5ex]
\sigma(z) 
\end{bmatrix}
$$

reason for which the softmax function is considered to be a generalization of the sigmoid function.

### Why the name "softmax"?

The name "softmax function" comes from the fact that $\Psi(z_1, z_2, \ldots, z_K)$ is actually related to both the $\operatorname{argmax}(z_1, z_2, \ldots, z_K)$ function,  that compares the input variables and returns the index $j$ for which $z_j$ is maximum,  and the $\operatorname{max}(z_1, z_2, \ldots, z_K)$ function, that outputs the value of the maximum. 

The relationship with $\operatorname{argmax}(z_1, z_2, \ldots, z_K)$ can be better seen noticing that $\sum_{l=1}^K e^{z_l}$, the denominator in Eq. (\ref{psi_component}),  can be roughly approximated by $\max_j e^{z_j}$. Hence, while $\operatorname{argmax}$ function would enforce the component $j$ to be equal to $1$, and the remaining to $0$,  the softmax function will produce an output such that $\forall k \neq j$, the value of $\psi_k(z_1, z_2, \dots, z_K)$ will tend to be low, but not exactly $0$,  and at index $j$,  $\psi_j(z_1, z_2, \dots, z_K)$ will tend to be high, though not exactly $1$. As a consequence, the softmax function is rather an approximation of the the $\operatorname{argmax}$ function, and thus many authors argue that is should be rather called "softargmax function". The advantage of using the softmax function is that it does not include the singular points of the $\operatorname{argmax}$ function. This is particularly beneficial in optimization tasks, since it allows to avoid both non-converging and diverging solutions.

On the other hand, the relationship with $\operatorname{max}(z_1, z_2, \ldots, z_K)$ can be better seen considering a  smooth approximation of this function, namely, the LogSumExp function

$$\operatorname{LSE}(z_1, z_2, \ldots, z_K) = \log\left(\sum_{k=1}^K e^{z_k}\right)$$

 For example, if $K=2$, evaluating at $z_1 = 0$ and $z_2 = 2$, we have $\operatorname{max}(0, z)$, that can be approximated with $\operatorname{LSE}(0, z) = \log\left(1 + e^z\right)$, as can be seen in the figure below.

Taking the derivative of $\operatorname{LSE}(z_1, z_2, \ldots, z_K)$ with respect to $z_k$, we find no other than $\psi_k(z_1, z_2, \ldots, z_K)$, the $k$th component of $\Psi(z_1, z_2, \ldots, z_K)$. Since this holds for any component, it is trivial to see that $\Psi(z_1, z_2, \ldots, z_K)$ is the gradient of $\operatorname{LSE}(z_1, z_2, \ldots, z_K)$
 
$$\frac{\partial \operatorname{LSE}(z_1, z_2, \ldots, z_K)}{\partial z_k} =  \psi_k(z_1, z_2, \ldots, z_K) $$

$$\Longrightarrow\Psi(z_1, z_2, \ldots, z_K) = \nabla\operatorname{LSE}(z_1, z_2, \ldots, z_K)$$

thus providing another interpretation of the softmax function.

### Partial derivatives

Since $\Psi(z_1, z_2, \ldots, z_K)$ is a vector function, rather than a unique derivative, it has multiple. The Jacobian matrix gathers all partial derivatives

$$\mathrm{J}_\Psi = \begin{bmatrix}
\frac{\partial \psi_1}{\partial z_1}& \frac{\partial \psi_1}{\partial z_2} & \cdots & \frac{\partial \psi_1}{\partial z_K} 
\\[1.5ex]
\frac{\partial \psi_2}{\partial z_1} & \frac{\partial \psi_2}{\partial z_2} & \cdots & \frac{\partial \psi_2}{\partial z_K} \\[1.5ex] 
\vdots & \vdots & \ddots & \vdots \\[1.5ex]
\frac{\partial \psi_K}{\partial z_1} & \frac{\partial \psi_K}{\partial z_2} & \cdots & \frac{\partial \psi_K}{\partial z_K} \\
\end{bmatrix}$$

Leveraging that the components of $\Psi$ result from similar functions, we can find all elements of $\mathrm{J}\_\Psi$ calculating a generic partial derivative, e.g.  of $\psi_k$ with respect to $z_j$. Letting $\delta_{jk}$ denote the Kronecker delta, such that

$$\delta_{jk} = 
\begin{cases}
    1, & \text{if } j = k\\
    0, & \text{otherwise}
\end{cases}
 $$

then we can write

$$\frac{\partial \psi_k}{\partial z_j} = \frac{e^{z_k} \delta_{jk} \sum_{l=1}^K e^{z_l} - e^{z_k} e^{z_j}}
{\left(\sum_{l=1}^K e^{z_l}\right)^2} = \frac{e^{z_k}}
{\sum_{l=1}^K e^{z_l}} \cdot \frac{\delta_{jk} \sum_{l=1}^K e^{z_l} - e^{z_j}}
{\sum_{l=1}^K e^{z_l}} $$

$$\Longrightarrow \frac{\partial \psi_k}{\partial z_j} = \psi_k (\delta_{jk} - \psi_j) \label{derivativeSoftmax}$$

We will later use Eq. (\ref{derivativeSoftmax}) to minimize the loss function, which we yet need to define.

## Estimating the probability distribution

In softmax regression, the output of a softmax function is used to represent the probability distribution of the categorical variable $y$. For this, we can define $\psi_k$ as the probability that $y$ may belong to class $k$, i.e. $\hat{p_k}(\mathbf{x}) = \hat{P}(y = k \;\vert\; \mathbf{x}) =  \psi_k(z_1, z_2, \ldots, z_K)$.

In addition, we define the input components of the softmax function  as  $z_k$ = $\mathbf{w_k}^\intercal \mathbf{x} + b_k$, where $\mathbf{x}$ is a sample, $\mathbf{w_k}$ is a weighting vector and $b_k$ is an intercept. To ease the notation, since we should now write $\psi_k(\mathbf{w_1}^\intercal \mathbf{x} + b_1, \mathbf{w_2}^\intercal \mathbf{x} + b_2, \ldots, \mathbf{w_K}^\intercal \mathbf{x} + b_K)$, we define 

$$\mathbf{z} =
\begin{bmatrix}
z_1 \\
z_2 \\
\vdots \\
z_K \\
\end{bmatrix}\quad
\mathbf{W} = 
\begin{bmatrix}
\mid & \mid & & \mid \\
\mathbf{w_1} & \mathbf{w_2} & \cdots & \mathbf{w_K}\\
\mid  & \mid & & \mid \\
\end{bmatrix}\quad 
\mathbf{b} =
\begin{bmatrix}
b_1 \\
b_2 \\
\vdots \\
b_K \\
\end{bmatrix}
$$

and express the same as $\left. \psi_k(\mathbf{z})\right\rvert_{\mathbf{z} = \mathbf{W}^\intercal \mathbf{x} + \mathbf{b}} = \psi_k\left(\mathbf{W}^\intercal \mathbf{x} + \mathbf{b}\right)$.

The objective of softmax regression then becomes tuning the $K$ weighting vectors and $K$ intercepts composing $\mathbf{W}$ and $\mathbf{b}$, respectively,  to achieve a "good" classification performance.

### One-hot encoding

The target variable $y$ may take a value from $1$ to $K$. An alternative representation is to express the output variable as a vector $\mathbf{y} \in \mathbb{R}^{K \times 1}$

$$\mathbf{y}  = \begin{bmatrix}
y_1 \\
y_2 \\
\vdots \\
y_K \\
\end{bmatrix}
$$

such that for a sample $\mathbf{x}^{(i)}$, we have

$$y_k = 
\begin{cases}
    1, & \text{if } y^{(i)} = k\\
    0, & \text{otherwise}
\end{cases}
 $$

In other words, the components of $\mathbf{y}$ are binary variables, and for each sample, all components are null, except the one whose index coincides with the class of the sample.  In general, we say that $y$ is now **one-hot encoded**, and this allows to straightforwardly compare the estimated probabilities delivered by $\left.\Psi (\mathbf{z})\right\rvert_{\mathbf{z} = \mathbf{W}^\intercal \mathbf{x} + \mathbf{b}}$ with the expected ones according to the value of $y$.

## Generalizing the cross-entropy loss function

Relying on a labeled one-hot encoded training set of $m$ i.i.d. samples $X = \left\\{(\mathbf{x}^{(1)}, \mathbf{y}^{(1)}), \mathbf{x}^{(2)}, \mathbf{y}^{(2)}), \ldots, (\mathbf{x}^{(m)}, \mathbf{y}^{(m)})\right\\}$, we can find the optimal values for $\\{\mathbf{w_1}, \mathbf{w_2}, \ldots, \mathbf{w_K}\\}$ and $\\{b_1, b_2, \ldots, b_K\\}$ relying on the maximum likelihood estimation method. In particular, each $\mathbf{y}^{(i)}$ follows a generalized Bernoulli distribution 

$$P\left(\mathbf{y}^{(i)} \;\vert\; \mathbf{x}^{(i)}\right) = \prod_{k=1}^K \left(p_k(\mathbf{x}^{(i)})\right)^{y_k^{(i)}}$$

Recalling that we are estimating $p_k(\mathbf{x}^{(i)})$ as $\hat{p_k}(\mathbf{x}^{(i)}, \mathbf{W}, \mathbf{b}) = \psi_k\left(\mathbf{W}^\intercal \mathbf{x}^{(i)} + \mathbf{b}\right)$, and considering the cost function $J(X, \mathbf{W}, \mathbf{b})$ as the negative version of the log-likelihood averaged over the $m$ samples of the dataset, then we have

$$J(X, \mathbf{W}, \mathbf{b}) = -\frac{1}{m}\sum_{i = 1}^m \sum_{k=1}^K y_k^{(i)} \log\left(\psi_k\left(\mathbf{W}^\intercal \mathbf{x}^{(i)} + \mathbf{b}\right)\right) $$

which, similar to what happens with logistic regression, resembles the cross-entropy function, this time evaluated comparing $\mathbf{y}$ with $\left. \Psi(\mathbf{z})\right\rvert_{\mathbf{z} = \mathbf{W}^\intercal \mathbf{x} + \mathbf{b}}$.

## Optimizing the parameters

To find the optimal value of $\mathbf{W}$ and $\mathbf{b}$, we need to minimize the cost function $J(X, \mathbf{W}, \mathbf{b})$. Relying on Eq. (\ref{derivativeSoftmax}) and further applying some algebra, it is simple to show that 

$$
\begin{align}
\frac{\partial J(X, \mathbf{W}, \mathbf{b})}{\partial \mathbf{w_k}} &= \frac{1}{m} \sum_{i=1}^m \mathbf{x}^{(i)}\left(\psi_k\left(\mathbf{W}^\intercal \mathbf{x}^{(i)} + \mathbf{b}\right) - y_k^{(i)}\right) \label{dJdwk-SM}\\
\frac{\partial J(X, \mathbf{W}, \mathbf{b})}{\partial b_k} &= \frac{1}{m} \sum_{i=1}^m \left(\psi_k\left(\mathbf{W}^\intercal \mathbf{x}^{(i)} + \mathbf{b}\right) - y_k^{(i)}\right) \label{dJdbk-SM}
\end{align}
$$

As with logistic regression, these equations cannot be solved in an exact form, and thus the cost function $J(X, \mathbf{W}, \mathbf{b})$ needs to be minimized relying on an optimization algorithm, e.g. the gradient descent algorithm.