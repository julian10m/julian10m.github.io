---
layout: post
title: Linear and Quadratic Discriminant Analysis 
---

Linear discriminant analysis (LDA) and quadratic discriminant analysis (QDA) are supervised machine learning algorithms used for multinomial classification tasks.  Both LDA and QDA estimate $P(y \;\vert\;\mathbf{x})$ relying on Bayes theorem, i.e., first calculating $P(\mathbf{x} \;\vert\; y)$, and  eventually $P(y)$ too. For each class $k \in \\{1, \ldots, K\\}$, LDA assumes that features follow a Gaussian distribution $N(\boldsymbol{\mu_k}, \Sigma)$, while for QDA they are of the type $N(\boldsymbol{\mu_k}, \Sigma_k)$. Relying on a training set, both LDA and QDA fit the mean value of each class. Moreover, while LDA fits a unique covariance matrix assumed to be shared by all classes, QDA  allows to fit one per class. As a consequence, while LDA may only draw linear decision boundaries, QDA is able to produce quadratic ones. Once the Gaussian distributions are known, and thus $P(\mathbf{x} \;\vert\; y)$ may be evaluated, assigning a label to new samples is straightforward.

## Gaussian Distributions

Since LDA and QDA assume that features of each class follow Gaussian distributions, before diving into these algorithms, it is actually convenient to first understand how to characterize a random variable $\mathit{X}$ that has either a univariate or multivariate Gaussian distribution.

###  Case Univariate

In this case, $\mathit{X}$ has a one-dimensional Gaussian distribution $\mathit{X} \sim N(\mu, \sigma^2)$, i.e.,

$$f_\mathit{X}(x \;\vert\; \mu, \sigma) = \frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{1}{2\sigma^2}(x-\mu)^2\right)$$

where $\mu$ is the mean or expectation of $\mathit{X}$, i.e., $\mu = \mathbb{E}[\mathit{X}]$, and $\sigma^2$ is the variance of $\mathit{X}$ , i.e., $\sigma^2 =V[\mathit{X}]$.

To characterize $\mathit{x}$, according to the shape of $f_\mathit{X}(x \;\vert\; \mu, \sigma)$, we only need to determine $\mu$ and $\sigma$. To estimate their values (for $\sigma$ we actually focus on $\sigma^2$) with a training set of $m$ i.i.d. samples $X = \left\\{x^{(1)},  \ldots, x^{(m)} \right\\}$, we can compute the log-likelihood 


$$\begin{align}
l(X, \mu, \sigma^2) &= \sum_{i=1}^m\log\left(f_\mathit{X} (x^{(i)}\vert\; \mu, \sigma)\right)\\
&= -\frac{m}{2}\log(2\pi) - \frac{m}{2}\log(\sigma^2)-\frac{1}{2\sigma^2}\exp{\left(\sum_{i=1}^m\left(x^{(i)}-\mu\right)^2\right)}
\end{align}
$$

To find $\hat{\mu}$ and $\hat{\sigma^2}$, we can calculate the partial derivatives of $l(X, \mu, \sigma^2)$ with respect to $\mu$ and $\sigma^2$, and set them to zero. Applying some algebra, it can be shown that

$$\begin{align}
\hat{\mu} &= \frac{1}{m}\sum_{i=1}^m x^{(i)}\\
\hat{\sigma^2} &= \frac{1}{m}\sum_{i=1}^m \left(x^{(i)} - \mu\right)^2
\end{align}
$$

The way it is defined, $\hat{\mu}$ can be directly computed from the samples, and thus is known as the sample mean. On the other hand, the variance estimator $\hat{\sigma^2}$ has the problem that it depends on the "real" value of $\mu$, that is actually unknown. Hence, in general, $\hat{\sigma^2}$ is approximated by the sample variance, which only requires replacing $\mu$ by $\hat{\mu}$ in the previous expression, i.e.,

$$\hat{\sigma^2} = \frac{1}{m}\sum_{i=1}^m \left(x^{(i)} - \hat{\mu}\right)^2$$

Since $E[\hat{\sigma^2}] = \frac{m-1}{m}\sigma^2\neq \sigma^2$,
then the sample variance is a biased estimator. We can define an unbiased estimator $\tilde{\sigma^2}$ as follows

$$\begin{align}
\tilde{\sigma^2} &= \frac{m}{m-1}\hat{\sigma^2}\\
&= \frac{1}{m-1}\sum_{i=1}^m \left(x^{(i)} - \hat{\mu}\right)^2
\end{align}
$$

Once $\hat{\mu}$ and $\tilde{\sigma^2}$ are computed, we can approximate the behavior of $\mathit{X}$ with that of a random variable with a normal distribution $N(\hat{\mu}, \tilde{\sigma^2})$.

###  Case Multivariate

In these scenarios, $\mathit{X}$ has a n-dimensional Gaussian distribution $\mathit{X} \sim N(\boldsymbol{\mu}, \Sigma)$, i.e.,

$$f_\mathit{X}(\mathbf{x} \;\vert\; \boldsymbol{\mu}, \Sigma) = \frac{1}{(2\pi)^{n/2}|\Sigma|^{1/2}}\,\exp\left(-\frac{1}{2}(\mathbf{x}-\boldsymbol{\mu})^\intercal~\Sigma^{-1}~(\mathbf{x}-\boldsymbol{\mu})\right)$$

where $\mathbf{x} \in \mathbb{R}^{n\times1}$ is a vector that may take any values, $\boldsymbol{\mu} \in \mathbb{R}^{n\times1}$ is the mean or expectation of $\mathit{X}$, i.e., $E[\mathit{X}]=\boldsymbol{\mu}$, and $\Sigma \in \mathbb{R}^{n \times n}$ is the covariance matrix of $\mathit{X}$, i.e., $\Sigma = E[(\mathit{X}-\boldsymbol{\mu})(\mathit{X}-\boldsymbol{\mu})^\intercal]$.

For a training set of $m$ i.i.d. samples $X = \left\\{\mathbf{x}^{(1)},  \ldots, \mathbf{x}^{(m)} \right\\}$, following the same reasoning as for the univariate case, it is straightforward to show that

$$\begin{align}
\hat{\boldsymbol{\mu}} &= \frac{1}{m}\sum_{i=1}^m \mathbf{x}^{(i)}\\
\hat{\Sigma} &= \frac{1}{m-1}\sum_{i=1}^m (\mathbf{x}^{(i)} - \hat{\boldsymbol{\mu}})(\mathbf{x}^{(i)} - \hat{\boldsymbol{\mu}})^\intercal
\end{align}
$$

Then we can emulate the behavior of $\mathit{X}$ as that of random variable with a normal distribution $N(\hat{\boldsymbol{\mu}}, \hat{\Sigma})$.

## Quadratic Discriminant Analysis

For each class $k \in \\{1, \ldots, K\\}$, features follow a multivariate Gaussian distribution $N(\mu_k, \Sigma_k)$. Given a training dataset of $m$ labeled samples $X = \left\\{(x^{(1)}, y^{(1)}) \ldots, (x^{(m)}, y^{(m)})\right\\}$, the objective is to determine the mean value and covariance matrix of each class. 

Assuming that for each class $k$ there are $m_k$ i.i.d. samples, such that $m =\sum_{k=1}^K m_k$,  and that samples of each class only give information about the parameters of that particular class, then we can actually split the problem of estimating all the required parameters in $K$ problems, each aiming to estimate those parameters associated to a particular class.  To solve each of these problems, we can apply the same reasoning as we did before to characterize multivariate Gaussian distributions, i.e., we only need to compute

$$\begin{align}
\hat{\boldsymbol{\mu_k}} &= \frac{1}{m_k}\sum_{y^{(i)}=k}^m \mathbf{x}^{(i)}\\
\hat{\Sigma}_k &= \frac{1}{m_k-1}\sum_{y^{(i)}=k}^m (\mathbf{x}^{(i)} - \hat{\boldsymbol{\mu_k}})(\mathbf{x}^{(i)} - \hat{\boldsymbol{\mu_k}})^\intercal
\end{align}
$$

## Linear Discriminant Analysis

For each class $k$, features have a Gaussian distribution $N(\mu_k, \Sigma)$. Compared with QDA, less parameters need to be estimated, since $\Sigma$ is now modeled as being similar across all classes.

 Considering the same dataset as for QDA, the mean values of each class may be calculated as in the previous method. On the other hand, to estimate $\Sigma$,  the complete dataset, i.e., the $m$ samples, can be used to estimate its value. In particular, $\hat{\Sigma}$ can be obtained as 
   
$$\begin{align}
\hat{\Sigma} &=  \frac{\sum_{k=1}^K (m_k -1)\hat{\Sigma}_k}{\sum_{k=1}^K (m_k - 1)}\\
&=  \frac{1}{m - K}\sum_{k=1}^K \sum_{y^{(i)}=k} (\mathbf{x}^{(i)} - \hat{\boldsymbol{\mu_k}})(\mathbf{x}^{(i)} - \hat{\boldsymbol{\mu_k}})^\intercal
\end{align}
$$

which is known as the pooled covariance matrix.