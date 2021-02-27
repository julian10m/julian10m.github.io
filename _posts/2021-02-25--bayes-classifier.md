---
layout: post
title: Classification relying on Bayes or Bayes classifiers
---

The objective is to determine to which class $\hat{y}$ an unlabelled observation $x$ belongs to, out of $K$ available. We assume that $x$ is composed of $n$ features/predictors, i.e., $x=(x_1,\ldots,x_n)$.

## Bayes classifier
The Bayes classifier assigns an observation $x$ to class $y_k$ when the likelihood of this being true is maximum. In other words, $\hat{y} = y_k$ when $P(y = y_k/x)$ is largest , i.e. 

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}} P(y = y_k/x) $$

The Bayes classifier is actually the best classification algorithm: it can be probed that  assigning observations maximizing the probability a posteriori actually minimizes the average classification error. However, the problem with this classifier is that, **in general, $P(y/x)$ is not known**,  and hence the required computations cannot be performed.

## Bayes Theorem

Though $P(y = y_k/x)$ may be unknown, we can try to rewrite it as a function of other parameters. In particular, we can rely on Bayes theorem, that leveraging properties spanning conditional probabilities, expresses that: 

$$ P(y = y_k/x) = \frac{P(x/y = y_k) P(y = y_k)}{P(x)} $$

such that:
   - $P(y = y_k)$ is the probability a priori of class $y_k$
   - $P(y = y_k/x)$ is the probability a posteriori of class $y_k$ given $x$
   - $P(x/y = y_k)$ is the likelihood of $x$ given that $y = y_k$
   - $P(x)$ is the marginal likelihood of $x$, or model evidence
 
When trying to classify an observation $x$, since $x$  is already known, then $P(x)$ simply acts as a scaling constant with a value that does not depend on any particular class $y_k$. As a consequence, $P(y = y_k/x) \propto P(x/y = y_k) P(y = y_k)$, and therefore we can assign $x$ to the class $y_k$ such that 

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(x/y = y_k) P(y = y_k)$$

## Naive Bayes Classifier (NBC)

The Naive Bayes classifier assumes that the $n$ predictors or features are **mutually independent among themselves**, therefore 

$$P(x/y = y_k) = \prod_{i=1}^n P(x_i/y = y_k)$$

The decision rule that this classifier applies then is

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y = y_k)\prod_{i=1}^n P(x_i/y = y_k)$$

In particular, the shape of the probability distributions $P(x_i/y = y_k)$ depends on the particular problem being solved, leading to different models and solutions.

### Bernoulli-NBC

The features are boolean variables indicating the occurrence or not of an event, property, etc, i.e. $x_i \in {0,1}$. For each class $y_k$, the features distribute as Bernoulli variables, i.e., 

$$P(x_i /y = y_k) =  
\begin{cases}
    p_{ki},& \text{if } x_i = 1\\
    1 - p_{ki},              & \text{otherwise}
\end{cases}
 $$

where $p_{ik}$ is the probability that $x_i$ might occur for class $y_k$, i.e., $p_{ik} = P(x_i = 1/y = y_k)$.  We can rewrite $P(x_i /y = y_k)$ in a single formula as 

$$P(x_i /y = y_k) =  p_{ik}^{x_i}(1-p_{ik})^{1-{x_i}}$$

 Hence, our classifier assigns $\hat{y}$ according to the following rule 

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y = y_k)\prod_{i=1}^n  p_{ik}^{x_i}(1-p_{ik})^{1-{x_i}}$$

In practice $p_{ik}$ is usually estimated from a traning dataset, looking at the frequency with which $x_i$ occurs for each class $y_k$. That is

$$\hat{p_{ik}} = \frac{m_{i,k}}{m_k}$$

where:
   -  $m_k$ is the number of training samples for which $y = y_k$, and;
   -  $m_{i,k}$ represents, out of the samples for which $y=y_k$, the number for which $x_i = 1$.


Eventually, for some classes it may happen that $x_i = 1$ or $x_i=0$ for the $m_k$ training samples belonging to that class. In these situations, it holds that $\hat{p_{ik}}= 1$ and  $\hat{p_{ik}} = 0$, respectively. This poses a problem since $P(x_i / y = y_k$ could eventually become null. To avoid situations like this, where the sampling bias may lead to falsely infer that the likelihood of an event occurring is null, the **Laplace smoothing** is used

$$\hat{p_{ik}} = \frac{1 + m_{i,k}}{2 + m_k}$$



### Multinomial-NBC

Each feature represents the frequency or number of times that an event or property occurs, i.e $x_i \in \mathbb{N}_0$. In these cases, rather than expressing the distribution of each feature given the class,  it is actually convenient to express their joint distribution, since it has a known form. Indeed, $x$ distributes as a multinomial variable

$$P(x /y = y_k) = \frac{\sum_{i=1}^n x_i}{\prod_{i=1}^n x_i!}\prod_{i=1}^n p^{x_i}_{ik}$$

 where $p_{ik}$ is the probability that $x_i$ might occur for class $y_k$, i.e., $p_{ik} = P(x_i = 1/y = y_k)$. Hence, our classifier assigns $\hat{y}$ according to the following rule 
 
$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y = y_k) \frac{\sum_{i=1}^n x_i}{\prod_{i=1}^n x_i!}\prod_{i=1}^n p^{x_i}_{ik}$$

As with the Bernoulli-NBC, $p_{ik}$ is usually estimated from a training dataset. In particular, making use of the Laplace smoothing

$$\hat{p_{ik}} = \frac{1 + f_{i,k}}{n + f_k}$$

where:
   - $f_{i,k}$ is the frequency with which $x_i$ appears for class $y_k$,
   - $f_k= \sum_{i=1}^K f_{i,k}$, and;
   - $n$ is the total number of features.
 
It is important to notice that the multinomial distribution 

