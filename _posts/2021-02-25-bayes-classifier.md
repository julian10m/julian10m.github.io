---
layout: post
title: Classification relying on Bayes or Bayes classifiers
---

The objective is to determine to which class $\hat{y}$ an unlabelled observation $x$ belongs to, out of $K$ available. We assume that $x$ is composed of $n$ features/predictors, i.e., $x=(x_1,\ldots,x_n)$.

## Bayes classifier
The Bayes classifier assigns an observation $x$ to class $y_k$ when the likelihood of this being true is maximum. That is, given $x$, the classifier looks for the class that has more chances of having generated this sample, and labels it as belonging to that class. In mathematical terms, $\hat{y} = y_k$ when $P(y_k/x) = P(y = y_k/x) $ is largest , i.e. 

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}} P(y_k/x) $$

The Bayes classifier is thus said to maximize the probability a posteriori, where the name stands from the fact that $x$ is already known at this stage. 
It can be probed that, to solve classification problems, this decision rule is optimal, meaning that it actually minimizes the average classification error. However, the problem with this classifier is that, **in general, $P(y/x)$ is not known**,  and hence the required computations cannot be performed.

## Bayes Theorem

Though $P(y_k/x)$ may be unknown, we can try to rewrite it as a function of other parameters. In particular, we can rely on Bayes theorem which, leveraging properties spanning conditional probabilities, expresses that: 

$$ P(y_k/x) = \frac{P(x/y_k) P(y_k)}{P(x)} $$

such that:
   - $P(y_k)$ is the probability a priori of class $y_k$
   - $P(y_k/x)$ is the probability a posteriori of class $y_k$ given $x$
   - $P(x/y_k)$ is the likelihood of $x$ given that $y = y_k$
   - $P(x)$ is the marginal likelihood of $x$, or model evidence

The interpretation is quite nice: the chances of any class $y_k$ being the true class for $x$ depends not only on how likely it is that $x$ might have been generated by $y_k$, but also on how likely this was even before $x$ was drawn.
To classify, if no information about $x$ was given, we would rely on the probabilities a priori to perform the task.
However, once $x$ is known, we can further leverage this insight to make a better prediction.
In addition, note that if $y$ distributes as a uniform variable, since $P(y_k) = 1/K$ for all classes, then choosing the maximum a posteriori is actually equal to choosing the class that maximizes the likelihood of $x$.
 
Finally, note that when trying to classify the observation $x$, since $x$ is already known, then $P(x)$ simply acts as a scaling constant with a value that does not depend on any particular class $y_k$. As a consequence, $P(y_k/x) \propto P(x/y_k) P(y_k)$, and therefore we can assign $x$ to the class $y_k$ such that 

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(x/y_k) P(y_k)$$

## Naive Bayes Classifier (NBC)

The Naive Bayes classifier assumes that the $n$ predictors/features are **mutually independent**, therefore we can write their joint distribution as the product of the marginals, i.e.

$$P(x/y_k) = \prod_{i=1}^n P(x_i/y_k)$$

The decision rule that the NBC applies then can be expressed as

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y_k)\prod_{i=1}^n P(x_i/y_k)$$

In particular, the shape of the probability distributions $P(x_i/y_k)$ depends on the particular problem being solved, leading to different models and solutions.

The assumption of independence among the features/predictors made by the NBC is strong and naive. Due to this, the NBC usually estimates badly, however it is still often able to classify well. The success of the NBC is some applications, e.g. text classification, is associated to the fact that this classifier is (i) efficient, since the training can be performed in one pass over the training set, and (ii) effective, since the accuracy it achieves is relatively good. 

### Bernoulli-NBC

The features are boolean variables indicating the occurrence or not of an event, property, etc, i.e., $x_i \in {0,1}$. For each class $y_k$, the features distribute as Bernoulli variables, i.e., 

$$P(x_i /y_k) =  
\begin{cases}
    p_{ik},& \text{if } x_i = 1\\
    1 - p_{ik},              & \text{otherwise}
\end{cases}
 $$

where $p_{ik}$ is the probability that $x_i$ might occur for class $y_k$, i.e., $p_{ik} = P(x_i = 1/y_k)$.  We can rewrite $P(x_i /y_k)$ in a single formula as 

$$P(x_i /y_k) =  p_{ik}^{x_i}(1-p_{ik})^{1-{x_i}}$$

Hence, our classifier assigns $\hat{y}$ according to the following rule 

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y_k)\prod_{i=1}^n  p_{ik}^{x_i}(1-p_{ik})^{1-{x_i}}$$


In practice $p_{ik}$ and $P(y_k)$ are usually estimated from a traning dataset looking at the frequency with which class $y_k$ and $x_i=1$ occur on the training set and samples where $y = y_k$, respectively. This can be expressed as

$$P(y_k) = \frac{m_k}{\sum_{j=1}^K m_j}$$

$$\hat{p_{ik}} = \frac{m_{i,k}}{m_k}$$

where:
   -  $m_k$ is the number of training samples for which $y = y_k$, and;
   -  $m_{i,k}$ represents, out of the samples for which $y=y_k$, the number for which $x_i = 1$.

In many cases it is not rare to see that $P(y_k)$ is not estimated from the dataset: instead it is assumed that $y$ distributes as a uniform variable, i.e. $P(y_k) = 1/K$ for all classes. On the other hand, eventually, for the $m_k$ training samples of any given class, it may happen that $x_i = 1$ or $x_i=0$ for all of them. In these situations, we would then estimate $p_{ik}$ to be equal to 1 and 0, respectively. Overall, the problem is that this way of estimating $p_{ik}$ could potentially lead to estimate $P(x_i / y_k)$ as being null. To avoid situations like this, where the sampling bias may lead to falsely infer that the likelihood of an event occurring is null (and also overestimate the opposite), the **Laplace smoothing** is used

$$\hat{p_{ik}} = \frac{1 + m_{i,k}}{2 + m_k}$$

As can be seen, the constant that is added in the numerator prevents the aforementioned problem.




### Multinomial-NBC

Each feature represents the frequency or number of times that an event or property occurs, i.e $x_i \in \mathbb{N}_0$.  In these cases, rather than expressing the distribution of each feature given the class, it is actually convenient to express their joint distribution, since it has a known form. Indeed, $x$ distributes as a multinomial variable

$$P(x /y_k) = \frac{\sum_{i=1}^n x_i}{\prod_{i=1}^n x_i!}\prod_{i=1}^n p^{x_i}_{ik}$$

 where $p_{ik}$ is the probability that $x_i$ might occur for class $y_k$, i.e., $p_{ik} = P(x_i = 1/y_k)$. Hence, our classifier assigns $\hat{y}$ according to the following rule 
 
$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y_k) \frac{\sum_{i=1}^n x_i}{\prod_{i=1}^n x_i!}\prod_{i=1}^n p^{x_i}_{ik}$$

Note that once given $x$, the term $\frac{\sum_{i=1}^n x_i}{\prod_{i=1}^n x_i!}$  is a constant that does not depend on any particular class $y_k$. Therefore, the multinomial problem is all about finding the $\hat{y}$ such that

$$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y_k) \prod_{i=1}^n p^{x_i}_{ik}$$

As with the Bernoulli-NBC, $p_{ik}$ is usually estimated from a training dataset. In particular, making use of the Laplace smoothing

$$\hat{p_{ik}} = \frac{1 + f_{i,k}}{n + f_k}$$

where:
   - $f_{i,k}$ is the frequency with which $x_i$ appears for class $y_k$,
   - $f_k= \sum_{i=1}^K f_{i,k}$, and;
   - $n$ is the total number of features.


In addition, similar to the Bernoulli-NBC, the value of $P(y_k)$ is either assumed to be $1/K$ or estimated looking at the proportion of samples in the dataset for which $y= y_k$.
 
## Useful links:

   - [_Introduction to Information Retrieval_](https://nlp.stanford.edu/IR-book/pdf/13bayes.pdf), Chapter 13 Text classification & Naive Bayes, Manning _et al_. 
   -  [_Naive Bayes Classifier_](https://en.wikipedia.org/wiki/Naive_Bayes_classifier), Wikipedia.

   