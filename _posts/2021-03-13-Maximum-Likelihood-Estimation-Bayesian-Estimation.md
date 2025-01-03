---
layout: post
title: Maximum Likelihood and Bayesian Estimation
---

A training set $X$ composed of $m$ samples, i.e., $X = (x^{(1)}, \ldots, x^{(m)})$, can be modeled as the outcome of a random variable $\mathcal{X}$. If we knew how this variable behaves, we would then be able to characterize the dataset. The behavior of a random variable is determined by its probability distribution. Hence, we are interested in determining the distribution of $\mathcal{X}$

$$P_{\mathcal{X}}(X) = P_{\mathcal{X}}(x^{(1)}, \ldots, x^{(m)})$$

In general, however, estimating this probability function is not trivial. A compromise solution is to actually guess the underlying shape of $P_{\mathcal{X}}(X)$, i.e., propose a formula that describes $P_{\mathcal{X}}(X)$. This formula is usually parametrized, meaning that it depends on a set of parameters $\theta$. There are two possibilities:

 - $\theta$ is considered an unknown vector of constants that we would like to determine. In these cases, the method of momentums or maximum likelihood estimation may be applied.
 - $\theta$ is a random variable. Under this assumption, usually bayesian estimation is used.

## Maximum Likelihood Estimation (MLE)

This method tells us that the optimal value of the unknown constants $\theta$ is the one that maximizes the joint probability $P_{\mathcal{X}}(X, \theta)$, i.e.,

$$\hat{\theta}(X) = \underset{\theta}{\operatorname{arg max}}P_{\mathcal{X}}(X, \theta)$$

The question that MLE answers then can be put as "given that we have this dataset $X$, for which value of $\theta$ would it have been more likely to actually observe $X$?". When the parameters in the formula we proposed take the values of $\hat{\theta}(X)$, the odds of this event are maximized. 

To find $\hat{\theta}$(X), in the cases that $P_{\mathcal{X}}(X, \theta)$ is a differentiable function, we can simply look for 

$$\nabla_{\theta}\;P_{\mathcal{X}}(X, \theta) = 0$$

When the training samples are independent and identically distributed, $P_{\mathcal{X}}(X, \theta)$ is the product of the marginal distributions. For these cases, we can write 

$$\nabla_{\theta} \prod_{i=1}^m P_x(x^{(i)}, \theta) = 0$$

Since the logarithm is a monotonic increasing function, then the solutions to the last equation are the same that the ones for

$$\nabla_{\theta} \sum_{i=1}^m \log \bigg(P_x(x^{(i)}, \theta)\bigg) = 0$$

where $l(\theta; X) = \sum_{i=1}^m \log \bigg(P_x(x^{(i)}, \theta)\bigg)$ is referred to as the log-likelihood of $\theta$ according to the data $X$. 

Finally, a negative version of the log-likelihood may be described as a cost function $J(\theta; X)$ which we then seek to minimize, rather than maximize:

$$\hat{\theta}(X) = \underset{\theta}{\operatorname{arg max}}\;l(\theta; X) = \underset{\theta}{\operatorname{arg min}}\big(- l(\theta; X)\big) = \underset{\theta}{\operatorname{arg min}}J(\theta; X)$$












