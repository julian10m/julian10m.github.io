---
layout: post
title: Linear and Quadratic Discriminant Analysis 
---

Linear discriminant analysis (LDA) and quadratic discriminant analysis (QDA) are supervised machine learning algorithms used for multinomial classification tasks. For each class $k \in \\{1, \ldots, K\\}$, LDA assumes that features distribute as a Gaussian distribution $N(\boldsymbol{\mu_k}, \Sigma)$, while for QDA they are of the type $N(\boldsymbol{\mu_k}, \Sigma_k)$. In other words, with LDA, for each class, the features have their own mean values, but their variance and covariance is similar across all classes. On the other hand, QDA  assumes the same for the mean values, but allows to fit a covariance matrix per class. As a consequence, while LDA may only draw linear decision boundaries, QDA is able to produce quadratic ones.

## Gaussian Distributions

To study LDA and QDA, it is convenient to first analyze how to characterize a random variable $\mathfrak{X}$ that has either a univariate or multivariate Gaussian distribution.

###  Case Univariate

In this case $\mathfrak{X}$ has a one-dimensional Gaussian distribution $\mathfrak{X} \sim N(\mu, \sigma^2)$, i.e.,

$$f_\mathfrak{X}(x \;\vert\; \mu, \sigma) = \frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{1}{2\sigma^2}(x-\mu)^2\right)$$

where $\mu$ is the mean or expectation of $\mathfrak{X}$, i.e., $\mu = \mathbb{E}[\mathfrak{X}]$, and $\sigma^2$ is the variance of $\mathfrak{X}$ , i.e., $\sigma^2 =V[\mathfrak{X}]$.

To characterize $x$, it is sufficient to determine $\mu$ and $\sigma$. To estimate their values (for $\sigma$ we actually focus on $\sigma^2$) with a training set of $m$ i.i.d. samples $X = \left\\{x^{(1)},  \ldots, x^{(m)} \right\\}$,  we can compute the log-likelihood 

$$\begin{align}
l(X, \mu, \sigma^2) &= \sum_{i=1}^m\log\left(f_\mathfrak{X} (x^{(i)}\vert\; \mu, \sigma)\right)\\
&= -\frac{m}{2}\log(2\pi) - \frac{m}{2}\log(\sigma^2)-\frac{1}{2\sigma^2}\exp{\left(\sum_{i=1}^m\left(x^{(i)}-\mu\right)^2\right)}
\end{align}
$$

We can find $\hat{\mu}$ and $\hat{\sigma^2}$ setting to zero the partial derivatives of $l(X, \mu, \sigma^2)$  with respect to $\mu$ and $\sigma^2$. Applying some algebra, it can be shown that

$$\begin{align}
\hat{\mu} &= \frac{1}{m}\sum_{i=1}^m x^{(i)}\\
\hat{\sigma^2} &= \frac{1}{m}\sum_{i=1}^m \left(x^{(i)} - \mu\right)^2
\end{align}
$$

The way it is defined, $\hat{\mu}$ is known as the mean sample, can be directly computed from the samples. On the other hand, the variance estimator $\hat{\sigma^2}$ has the problem that it depends on the "real" value of $\mu$, that is actually unknown. Hence, in general, $\mu$ is approximated by $\hat{\mu}$, 

$$\hat{\sigma^2} = \frac{1}{m}\sum_{i=1}^m \left(x^{(i)} - \hat{\mu}\right)^2$$

which gives what is known as the sample variance.

Despite the previous trick allows us to compute $\hat{\sigma^2}$, the problem with the sample variance is that it is a biased estimator, since $E[\hat{\sigma^2}] = \frac{m-1}{m}\sigma^2\neq \sigma^2$. We can define a new unbiased estimator $\tilde{\sigma^2}$ such that

$$\begin{align}
\tilde{\sigma^2} &= \frac{m}{m-1}\hat{\sigma^2}\\
&= \frac{1}{m-1}\sum_{i=1}^m \left(x^{(i)} - \hat{\mu}\right)^2
\end{align}
$$

Then we can emulate the behavior of $\mathfrak{X}$ as that of random variable with a normal distribution $N(\hat{\mu}, \tilde{\sigma^2})$.

###  Case Multivariate

In these scenarios,  $\mathfrak{X}$ has a n-dimensional Gaussian distribution $\mathfrak{X} \sim N(\boldsymbol{\mu}, \Sigma)$, i.e.,

$$f_\mathfrak{X}(\mathbf{x} \;\vert\; \boldsymbol{\mu}, \Sigma) = \frac{1}{(2\pi)^{n/2}|\Sigma|^{1/2}}\,\exp\left(-\frac{1}{2}(\mathbf{x}-\boldsymbol{\mu})^\intercal~\Sigma^{-1}~(\mathbf{x}-\boldsymbol{\mu})\right)$$

where $\mathbf{x} \in \mathbb{R}^{n\times1}$ is a vector that may take any values, $\boldsymbol{\mu} \in \mathbb{R}^{n\times1}$ is the mean or expectation of $\mathfrak{X}$, i.e., $E[\mathfrak{X}]=\boldsymbol{\mu}$, and $\Sigma \in \mathbb{R}^{n \times n}$ is the covariance matrix of $\mathfrak{X}$, i.e., $\Sigma = E[(\mathfrak{X}-\boldsymbol{\mu})(\mathfrak{X}-\boldsymbol{\mu})^\intercal]$.

For a training set of $m$ i.i.d. samples $X = \left\\{\mathbf{x}^{(1)},  \ldots, \mathbf{x}^{(m)} \right\\}$,  following the same reasoning as for the univariate case, it is straightforward to show that

$$\begin{align}
\hat{\boldsymbol{\mu}} &= \frac{1}{m}\sum_{i=1}^m \mathbf{x}^{(i)}\\
\hat{\Sigma} &= \frac{1}{m-1}\sum_{i=1}^m (\mathbf{x}^{(i)} - \hat{\boldsymbol{\mu}})(\mathbf{x}^{(i)} - \hat{\boldsymbol{\mu}})^\intercal
\end{align}
$$

Then we can emulate the behavior of $\mathfrak{X}$ as that of random variable with a normal distribution $N(\hat{\boldsymbol{\mu}}, \hat{\Sigma})$.

## Linear Discriminant Analysis


For any class $k$, the only available feature $x$ distributes as a univariate Gaussian distribution $N(\mu_k, \sigma)$, i.e.,

$$f(x \;\vert\; y = k) = \frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{1}{2\sigma^2}(x-\mu_k)^2\right)$$

For a training dataset of $m$ i.i.d. labeled samples $X = \left\\{(x^{(1)}, y^{(1)}) \ldots, (x^{(m)}, y^{(m)})\right\\}$ we have

$$P\left(y^{(1)}, \ldots, y^{(m)} \Bigm\vert x^{(1)}, \ldots, x^{(m)}\right) \propto \prod_{i=1}^m f\left(x^{(i)}\;\vert\;\ y^{(i)}\right)$$
$$= (2\pi\sigma^2)^{-m/2}\exp\left(-\sum_{i=1}^m\frac{1}{2\sigma^2}(x-\mu_k)^2\right)
\end{align}
$$

$$f(x^{(1)} \;\vert\; y = k) = \frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{1}{2\sigma^2}(x-\mu_k)^2\right)$$

### Multivariate Gaussian Distributions

In this case,  $x\in \mathbb{R}^{N\times1}$ and is of the type $\mathbf{x} = (x_1, \ldots, x_N)^\intercal$
$$f(\mathbf{x} \;\vert\; y = k) = \frac{1}{(2\pi)^{N/2}|\Sigma|^{1/2}}\exp\left(-\frac{1}{2}(\mathbf{x}-\boldsymbol{\mu_k})^\intercal~\Sigma^{-1}~(\mathbf{x}-\boldsymbol{\mu_k})\right)$$
