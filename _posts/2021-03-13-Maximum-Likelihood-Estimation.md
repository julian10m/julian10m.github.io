---
layout: post
title: Maximum Likelihood Estimation (MLE)
---

A training set $X$ composed of $m$ samples, i.e., $X = (x^{(1)}, \ldots, x^{(m)})$, can be modeled as the outcome of a random variable $\mathcal{X}$. If we knew how this variable behaves, we would then be able to characterize the dataset. The behavior of a random variable is determined by its probability distribution. Hence, we are interested in determining the distribution of $\mathcal{X}$, which coincides with the joint probability of $(x^{(1)}, \ldots, x^{(m)})$

$$P_{\mathcal{X}}(X) = P_{\mathcal{X}}(x^{(1)}, \ldots, x^{(m)})$$

In general, however, estimating this probability function is not trivial. A compromise solution is to actually guess the underlying shape of $P_{\mathcal{X}}(X)$, i.e., propose a formula that describes $P_{\mathcal{X}}(X)$. This formula is usually parametrized, meaning that it depends on a set of parameters $\theta$. A priori, $\theta$ is unknown, but may be adjusted and, in fact, the objective is to determine its optimal value.

In particular, with maximum likelihood estimation, the question we want to answer is "given that we have this dataset $X$, for which value of $\theta$ would it have been more likely to actually observe $X$?".
This translates into looking for the value of $\theta$ that maximizes the joint probability $P_{\mathcal{X}}(X, \theta)$

$$\hat{\theta}(X) = \underset{\theta}{\operatorname{arg max}}P_{\mathcal{X}}(X, \theta)$$

This way, when the parameters in the formula we proposed take the values of $\hat{\theta}$, then the chances of obtaining the dataset that we actually have are maximized. To find $\hat{\theta}$, in the cases that $P_{\mathcal{X}}(X, \theta)$ is a differentiable function, we can simply look for 

$$\nabla_{\theta}P_{\mathcal{X}}(X, \theta) = 0$$

When the training samples we have are independent and identically distributed, then 




