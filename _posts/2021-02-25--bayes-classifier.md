---
layout: post
title: Classification relying on Bayes or Bayes classifiers
---

The objective is to determine to which class \(\hat{y}\) an unlabelled observation \(x\) belongs to, out of \(K\) available. We assume that \(x\) is composed of \(n\) features/predictors, i.e., \(x=(x_1,\ldots,x_n)\).

## Bayes classifier
The Bayes classifier assigns an observation $x$ to class $y_k$ when the likelihood of this being true is maximum. In other words, this is done when $P(y_k/x)$ is largest, i.e. $$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}} P(y_k/x) $$ The problem with this classifier is that, in general, $P(y_k/x)$ is not known. Hence, this reduces the possibilities of actually using this classifier on practice.

## Bayes Theorem

Though $P(y_k/x)$ may be unknown,  we can try to rewrite it as a function of other parameters. In particular, we can rely on Bayes theorem, that leveraging properties spanning conditional probabilities, expresses that: $$ P(y_k/x) = \frac{P(x/y_k) P(y_k)}{P(x)} $$ such that:
   - $P(y_k)$ is the probability a priori of class $y_k$
   - $P(y_k/x)$ is the probability a posteriori of class $y_k$ given $x$
   - $P(x/y_k)$ is the likelihood of $x$ given for class $y_k$
   - $P(x)$ is the marginal likelihood of $x$, or model evidence
 
When trying to classify an observation $x$, since $x$  is already known, then $P(x)$ simply acts as a scaling constant with a value that does not depend on any particular class $y_k$. As a consequence, $P(y_k/x) \propto P(x/y_k) P(y_k)$, and therefore we can assign $x$ to the class $y_k$ such that $$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(x/y_k) P(y_k)$$

## Naive Bayes

The Naive Bayes classifier assumes that the $n$ predictors or features are mutually independent among themselves, therefore $$P(x/y_k) = \prod_{i=1}^n P(x_i/y_k)$$

In conclusion, the classifier applies the following decision rule:  $$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y_k)\prod_{i=1}^n P(x_i/y_k)$$

In particular, the shape of the probability distributions $P(x_i/y_k)$ depends on the particular problem being solved, leading to different models and solutions.

### Bernoulli Naive Bayes Classifier

In this case, the features are boolean variables indicating the occurrence or not of an event, property, etc, i.e. $x_i \in \{0,1\}$. For each class $y_k$, the features distribute as Benoulli variables, i.e., $$P(x_i / y_k) = p_{ik}^{x_i}(1-p_{ik})^{1-{x_i}}$$ where $p_{ik} = P(x_i = 1/ y_k)$. Hence, our classifier assigns $\hat{y}$ according to the following rule $$\hat{y} = \underset{k\in\{1\ldots,K\}}{\operatorname{arg max}}P(y_k)\prod_{i=1}^n  p_{ik}^{x_i}(1-p_{ik})^{1-{x_i}}$$
