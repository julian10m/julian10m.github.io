---
layout: post
title: Logistic and Softmax Regression
---

<div align="center">

Logistic and softmax regression are supervised machine learning algorithms used for classification tasks. Given a categorical target variable $y$ that may take $K$ possible values, and an observation $\mathbf{x}$ composed of $N$ features/predictors, i.e., $\mathbf{x}=[x_1,\ldots,x_N]^\intercal$, these algorithms estimate the probability a posterior of $y$ given $\mathbf{x}$. Based on this estimation, these classifiers apply a decision rule that allows them to classify each sample, i.e., assign them an estimated class.

## Logistic Regression

This method is used for binary classification, i.e., cases where $K=2$. However, by combining multiple logistic regression classifiers, it is possible to extend their use to multinomial cases, i.e., scenarios where $K>2$.  

### Binary Classification

The target variable can only take two possibles values, e.g., "nice/ugly", "fun/boring", "win/lose", "red/blue", "apple/orange", "woman/man".  We usually use 0 and  1 to numerically represent these values, i.e.,  $y \in \left\\{0, 1\right\\}$.  This way,  in each respective scenario, $y=1$ may represent "yes",  "fun" and "win", "red", "apple" or "woman",  while $y=0$ be associated to "no", "boring", "lose", "blue", "orange" or "man". We can also assign the labels in the opposite order, this actually has no impact on the performance of the algorithm. 

#### Sigmoid Function

One of the main components of logistic regression is the sigmoid function $\sigma: \mathbb{R}\to (0, 1)$, defined as 

$$\sigma(z)= \frac{e^z}{1+e^z} = (1 + e^{-z})^{-1}\label{sigmoid}$$

According to Eq. (\ref{sigmoid}), $\sigma(z)$ always lays between 0 and 1. In particular, for large negative values of $z$, the term $e^{-z}$ becomes a large positive number, and thus $\sigma(z)$ tends to $0$. On the other hand, when $z$ takes large positive values, $e^{-z}$ approaches $0$, and thus $\sigma(z)$ is close to $1$. For $z=0$, since $e^0 = 1$, then $\sigma(0) = 1/2$. 

On the other hand, we can see that

$$\sigma'(z)=\frac{\mathrm{d}\sigma(z)}{\mathrm{d}z} = \frac{e^z(1+e^z) - e^z e^z}{(1 + e^z)^2} = \frac{e^z}{1 + e^z}\cdot\frac{1}{1 + e^z}$$

is always positive, and thus $\sigma(z)$ is a monotonically increasing function. In addition, noticing that

$$1 - \sigma(z) = 1 -\frac{e^z}{1+e^{-z}}  = \frac{1}{1+e^z}$$

then we can write 

$$\Longrightarrow \sigma'(z)= \sigma(z)\left(1 - \sigma(z)\right)\label{derivative}$$

Finally, it is simple to show that

$$\log\left(\frac{\sigma(z)}{1 - \sigma(z)}\right) = \log\left(e^z\right) = z\label{logit}$$

We will later come back to Eq. (\ref{derivative}) and (\ref{logit}).

#### A model to estimate probabilities

To classify any sample $\mathbf{x}$, assuming a probabilistic model, we are interested in estimating $P(y = 1 \;\vert\; \mathbf{x})$ and $P(y = 0 \;\vert\; \mathbf{x})$. To minimize the classification error, according to Bayesian decision theory, if $P(y=1 \;\vert\; \mathbf{x}) > P(y=0 \;\vert\; \mathbf{x})$, then our guess should be $\hat{y} = 1$, and $\hat{y} = 0$ otherwise. In practice, since $P(y = 1 \;\vert\; \mathbf{x}) + $P(y = 0 \;\vert\; \mathbf{x}) = 1$, then we actually only need to estimate one of these probabilities.

Logistic regression proposes using a sigmoid function to estimate $P\left(y = 1 \;\vert\; \mathbf{x}\right)$. Indeed, the fact that the image of the sigmoid function is constrained to the interval $(0, 1)$ allows to use this function to express the probability of an event. In particular, for a sample $\mathbf{x}$, the input to the sigmoid function is determined as the product between $\mathbf{x}$ and a weighting vector of $N$ parameters $\mathbf{w} = [w_1, \ldots, w_N]^\intercal$ plus a a bias or intercept $b$, i.e.,

$$\hat{p_1}(\mathbf{x}, \mathbf{w}, b)= \hat{P}\left(y = 1 \;\vert\; \mathbf{x}\right)= \sigma(\mathbf{w}^\intercal \mathbf{x} + b)$$

Since $\hat{p_1}(\mathbf{x}, \mathbf{w}, b)= \sigma(z)$ evaluated at $z = \mathbf{w}^\intercal \mathbf{x} + b$, then replacing in Eq. (\ref{logit}) we have

$$\log\left(\frac{\hat{p_1}(\mathbf{x}, \mathbf{w}, b)}{1 - \hat{p_1}(\mathbf{x}, \mathbf{w}, b)}\right) = \mathbf{w}^\intercal \mathbf{x} + b \label{logitDecisionBoundary}$$

where the left term is known as the log-odds or logit of $\hat{p_1}(\mathbf{x}, \mathbf{w}, b)$. 


Eq. (\ref{logitDecisionBoundary}) resembles the one we previously saw for linear regression, where $\hat{y} =  \mathbf{w}^\intercal \mathbf{x} + b$.  This gives us a hint of from where the logistic regression name comes: comparing both expressions, it is straightforward to conclude that logistic regression applies linear regression to the logits of $\hat{p_1}(\mathbf{x}, \mathbf{w}, b)$.  


#### Classification Rule and Decision Boundary

Despite the close relationship between linear and logistic regression, the first is used for regression tasks, but the latter in classification problems. Indeed, logistic regression additionally uses the following classification rule

$$\hat{y} =  
\begin{cases}
    1,& \text{if } \hat{p_1}(\mathbf{x}, \mathbf{w}, b) \geq 0.5\\
    0,& \text{otherwise}
\end{cases}
 $$

Since $\sigma(z) = 0.5$ only when $z = 0$, it is trivial to see that we can re-write the last condition as 

 $$\hat{y} =  
\begin{cases}
    1,& \text{if } \mathbf{w}^\intercal \mathbf{x} + b \geq 0\\
    0,& \text{otherwise}
\end{cases}
 $$

Analyzing this expression, we can see that there exists a decision boundary in $\mathbf{w}^\intercal \mathbf{x} + b = 0$. The values of $\mathbf{x}$ that satisfy this condition form an (affine) hyperplane in the sub-space generated by $\left\\{x_1, \ldots, x_n\right\\}$, where $b$ is the quantity by which this hyperplane is shifted from the origin. When $\mathbf{w}$ and $b$ are known, if $\mathbf{x}$ is such that the computation of $\mathbf{w}^\intercal \mathbf{x} + b$ is greater than $0$, then $\hat{y} = 1$, and  $\hat{y} = 0$ otherwise.

#### Cross-Entropy Loss Function

The optimal values of $\mathbf{w}$ and $b$ can be estimated relying on the maximum likelihood estimation method. Given a training set $X$ of $m$ labeled i.i.d. samples, i.e. $X = \left\\{(\mathbf{x}^{(1)}, y^{(1)}) \ldots, (\mathbf{x}^{(m)}, y^{(m)})\right\\}$ , modelling $y$ as a Bernoulli variable such that $p= \hat{p_1}(\mathbf{x}, \mathbf{w}, b)$, then the joint distribution for the dataset is

$$\begin{align}
P\left(y^{(1)}, \ldots, y^{(m)} \;\vert\; \mathbf{x}^{(1)}, \ldots, \mathbf{x}^{(m)}\right) &= \prod_{i=1}^m P\left(y^{(i)}\;\vert\;\ \mathbf{x}^{(i)}\right) \\
&= \prod_{i=1}^m \left(\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right)^{y^{(i)}} \left(1 -\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right)^{(1-y^{(i)})}
\end{align}
$$

where $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)$ is the estimated probability that sample $\mathbf{x}^{(i)}$ might belong to class $y=1$ and $y^{(i)}$ is the class to which $\mathbf{x}^{(i)}$ actually belongs to.

The log-likelihood, that we seek to maximize, can then be written as

$$l(X, \mathbf{w}, b) = \sum_{i=1}^m \left(y^{(i)} \log \left(\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right) + (1 - y^{(i)})\log\left(1 -\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right)\right)$$

Recalling we have modelled $\hat{p_1}(\mathbf{x}, \mathbf{w}, b) = \sigma(\mathbf{w}^\intercal \mathbf{x} + b)$, and defining the cost function $J(X, \mathbf{w}, b)$ as the negative version of the log-likelihood averaged over the $m$ samples composing the dataset, then

$$\begin{align}
J(X, \mathbf{w}, b) &=  - \frac{1}{m}l(X, w, b) \\
& = -\frac{1}{m}\sum_{i = 1}^m \left(y^{(i)} \log\left(\sigma(\mathbf{w}^\intercal \mathbf{x} + b)\right)+ (1 - y^{(i)}) \log\left(1 - \sigma(\mathbf{w}^\intercal \mathbf{x} + b)\right)\right) \label{cost-function}
\end{align}
$$

Since this mathematical expression resembles the one used in information theory to obtain the "cross-entropy" between two probability distributions, then this cost function is usually referred to as the **cross-entropy loss function**. An important, and convenient, characteristic of this function that it is convex, i.e., it has a minimum, and this minimum is unique and thus absolute.

#### Interpreting the Loss Function

Despite the cost function $J(X, \mathbf{w}, b)$ looks intricate, by further developing its expression, we can find a very intuitive way to understand it. For this, we need to note that, for any index $i$, then $y^{(i)}$ can only evaluate to $1$ or $0$. Therefore, for each sample $(\mathbf{x}^{(i)}, y^{(i)})$, only one term out of the two possible contributes to the cost function. We can use this fact to compress the expression of $J(X, \mathbf{w}, b)$ as follows

$$J(X, \mathbf{w}, b) = \sum_{i=1}^m c\left(y^{(i)}, \hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) \right)$$

where 

$$c\left( y^{(i)}, \hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right) = 
\begin{cases}
    - \log\left(\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right),& \text{if } y^{(i)} = 1\\
    - \log\left(1 - \hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right),& \text{otherwise}
\end{cases}
 $$

For example, when $y^{(i)} = 1$, if $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) = 1$, then the cost contributed by sample $(\mathbf{x}^{(i)}, y^{(i)})$ is 0. However, on the opposite extreme, if $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) = 0$, then the cost would become infinite. For intermediate values, the logarithm in the expression leads to penalize more the "worse" decisions, e.g., for $y^{(i)} = 1$, the smaller $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)$ is, the much larger $\log\left(\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right)$ will be.  In addition, analyzing the expression, we see that the cost for $y^{(i)} = 0$ is a mirrored copy of that of $y^{(i)} = 1$: the curve is similar, but rather penalizing more severely the more that $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)$ is closer to $1$.

Finally, it must be noted that, even if our classifier would have made correct classifications, i.e. estimated $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) > 0.5$ in a case where $y^{(i)} = 1$, we are still increasing the cost function. In other words, the cost function does not penalize mistakes, but more generally the fact that the classifier makes "doubtful" classifications. In other words, for all samples $\mathbf{x}^{(i)}$ and $\mathbf{x}^{(j)}$ for which $y^{(i)}=1$ and $y^{(j)}=0$, we would want our classifier not to hesitate, i.e., to end up computing $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) = 1$ and $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) = 0$, respectively.
This means that for all samples where the classifier was not completely "convinced" or "sure" of its choice, we should increase $J(X, \mathbf{w}, b)$.
In particular, minimizing the cross-entropy loss function tends to do this, thus generating estimations that resemble as closely as possible the "ideal" distribution.

#### Optimizing the parameters 

To find the optimal value of $\mathbf{w}$ and $b$, we need to minimize the cost function $J(X, \mathbf{w}, b)$. Analyzing Eq. (\ref{cost-function}), $J(X, \mathbf{w}, b)$ is differentiable, hence we find its minimum looking for the point where all the partial derivatives become null. It is simple to show, noticing that $\log(f(x)) = f'(x)/f(x)$ and considering the property of Eq. (\ref{derivative}), that

$$\begin{align}
\frac{\partial J(X, \mathbf{w}, b)}{\partial b} &= \frac{1}{m} \sum_{i=1}^m \left(\sigma(\mathbf{w}^\intercal \mathbf{x} + b) - y^{(i)}\right) \\
\frac{\partial J(X, \mathbf{w}, b)}{\partial w_j} &= \frac{1}{m} \sum_{i=1}^m x_j^{(i)}\left(\sigma(\mathbf{w}^\intercal \mathbf{x} + b) - y^{(i)}\right)
\end{align}
$$

Unfortunately, setting these equations to zero results into what are called transcendental equations, i.e., equations for which there is no closed form to solve them. To overcome this limitation, the only option left is to rely on numerical solutions, such as that provided by the gradient descent algorithm.  Note that since $J(X, \mathbf{w}, b)$ is convex, converging towards the only, and thus optimal, minimum is ensured. During the training, the values of $\mathbf{w}$ and $b$ will be iteratively updated, hence modifying the positioning of the hyperplane that serves as decision boundary for the classification problem. Hopefully, at the end of the process, the hyperplane will end up "well" located, i.e., for most training samples, the estimations and classifications the algorithm performs will be reasonably good. 

To implement the gradient descent algorithm, it is convenient to have a vectorized version of the functions we have studied. Defining 

$$X^\intercal = \begin{bmatrix}
1 & 1 & \cdots & 1 \\
\mid & \mid & & \mid \\
\mathbf{x}^{(1)} & \mathbf{x}^{(2)} & \cdots & \mathbf{x}^{(m)}\\
\mid  & \mid & & \mid \\
\end{bmatrix}\quad
\Theta  = \begin{bmatrix}
b \\
\mathbf{w} \\
\end{bmatrix}\quad
\mathbf{y}  = \begin{bmatrix}
y^{(1)} \\
y^{(2)} \\
\vdots \\
y^{(m)} \\
\end{bmatrix}
$$

we can then write 

$$J(X, \Theta) = -\frac{1}{m}\Big(\mathbf{y}^\intercal \log\left(\sigma(X \Theta)\right)+ (1 - \mathbf{y}^\intercal) \log\left(1 - \sigma(X \Theta)\right)\Big)$$

$$\nabla J(X, \Theta) = \frac{1}{m} X^\intercal\left(\sigma(X \Theta) - \mathbf{y}\right)$$


Note that the gradient descent algorithm represents only one option out of the multiple optimization algorithms that can be used.

###  Multinomial Classification

A logistic regression classifier only works for binary classification problems. On the other hand, when $y$ may take more than two values, i.e., $K>2$, we can use multiple logistic regression classifiers. More specifically, the proposal is to divide and conquer: instead of trying to come up with an algorithm that directly assigns the correct label to a sample $\mathbf{x}$, the results of multiple logistic regression classifiers that solve simpler binary classification problems are aggregated and used to reach a conclusion. 

In particular, two different approaches exist to combine multiple classifiers: one-vs-the-rest (OvR), also called one-vs-all (OvA), and one-vs-one (OvO). In the following, we assume we have a training dataset $X$, composed of $m$ labeled samples, i.e., $X = \left\\{(\mathbf{x}^{(1)}, y^{(1)}), \ldots, (\mathbf{x}^{(m)}, y^{(m)})\right\\}$. Moreover,  we can assume that, for each class $k \in \\{1, \ldots, K\\}$, the dataset contains $m_k$ samples of that class, such that $m = \sum_{k=1}^K m_k$.

#### OvR/OvA

The multinomial classification problem is divided in $K$ binary estimation problems, and then one decision rule is applied. 

Each logistic regression classifier focuses on one specific label, estimating the chances that this might be the correct one for the sample being analyzed. To determine this, for any training sample $(\mathbf{x}^{(i)}, y^{(i)})$, the estimator related to label $k$ re-labels the samples in a variable $z$ such that

$$z^{(i)} =  
\begin{cases}
    1,& \text{if } y^{(i)} = k\\
    0,& \text{otherwise}
\end{cases}
 $$

This way, while the $m_k$ samples of class $k$ remain unaltered, the remaining $m - m_k$ samples belonging to the $K-1$ remaining labels are aggregated into a unique class. Each classifier is then trained on each of these re-labeled datasets.

To classify a new sample $\mathbf{x}^{(i)}$, the probability $P(z^{(i)} = 1 \;\vert\; \mathbf{x}^{(i)})$ for the $k^{th}$estimator is not other than $P(y^{(i)} = k \;\vert\; \mathbf{x})$, thus once these values are computed, $\mathbf{x}^{(i)}$ is assigned to label $k$ when the $k^{th}$ estimator outputs the largest value.

#### OvO

The multinomial classification task is divided in multiple binary classification problems, and the decision is reached by majority voting. 

Each logistic regression classifier compares two classes at a time, as if the remaining did not exist, e.g. the classifier focusing on labels $k_1$ and $k_2$ only uses their corresponding $m_{k_1}$ and $m_{k_2}$ samples, assigning $z^{(i)}=1$ to sample $\mathbf{x}^{(i)}$ when $y^{(i)} = k_1$, and $z^{(i)} = 0$ otherwise. In total, for $K$ labels, there are ${k \choose 2} = \frac{K(K-1)}{2}$ ways of combining them (there are $K$ labels, each of which can be linked to each of the $K-1$ remaining labels, but this would count twice each of the links, so we need to divide by 2), hence this is the number of logistic regression classifiers that are needed. 

When classifying a new sample $\mathbf{x}^{(i)}$, then $K(K-1)/2$ classifications are performed, and ultimately $\mathbf{x}^{(i)}$ is assigned to the class $k \in \\{1, \ldots, K\\}$ that results most voted across all classifiers.


## Softmax Regression

 While workarounds such as OvR/OvA or OvO allow to use logistic regression for multinomial classification tasks, softmax regression is a method specifically conceived for this purpose. In particular, softmax regression replaces the sigmoid function used in logistic regression with the softmax function, a function that rather has both $K$ weighting vectors $\\{\mathbf{w_1}, \mathbf{w_2}, \ldots, \mathbf{w_K}\\}$ and intercepts $\\{b_1, b_2, \ldots, b_K\\}$ as parameters, allowing it to estimate one probability per class instead of only one in total. 

The softmax function is a vector function that, given a sample $\mathbf{x} \in \mathbb{R}^{N\times1}$, returns a vector $\mathbf{s} \in \mathbb{R}^{K\times1}$. In particular, for each component $s_k$, we have that

$$s_k = \frac{e^{z_k}}{\sum_{l=1}^K e^{z_l}}$$

where:
- $0 \leq  s_k \leq 1$ and $\sum_{k=1} s_k = 1$,  making this function suitable to represent a probability distribution, and;
- $z_k$ = $\mathbf{w_k}^\intercal \mathbf{x} + b_k$, where $\mathbf{w_k}$ is a weighting vector and $b_k$ is an intercept defined for the component $k$.

The name softmax comes from the fact that this function works as a smoothed version of $\operatorname{argmax}\_je^{z_j}$: since $\sum_{l=1}^K e^{z_l}$ can be roughly approximated by $\max_j e^{z_j}$, then the value at index $j$, namely $s_j$, will tend to be much higher than that of the remaining components, but not exactly $1$, as would be enforced by $\operatorname{argmax}\_je^{z_j}$.  While the $\operatorname{argmax}$ function  usually contains singular points, the advantage of the softmax function is that it is actually a differentiable function over all points. 

In softmax regression, the output of a softmax function is used to represent the probability distribution of the categorical variable $y$. For this, we can define $s_k$ as the probability that $y$ may belong to class $k$, i.e. $\hat{p_k} = \hat{P}(y = k \;\vert\; \mathbf{x}) = s_k$. The objective then becomes tuning the $K$ weighting vectors and $K$ intercepts correctly, in order to achieve a good classification performance.

To train our model, $y$ needs to be one-hot encoded

$$mathbf{y}  = \begin{bmatrix}
y_1 \\
y_2 \\
\vdots \\
y_k \\
\end{bmatrix}
$$

Indeed, rather than taking a value from $1$ to $K$, the output variable is represented as a vector $\mathbf{y} \in \mathbb{R}^{K \times 1}$ such that if $\mathbf{x}$ belongs to class $k$, then $y_k^{(i)} = 1$ and $\forall j \neq k, \,y_j^{(i)} = 0$. In other words, the components of $\mathbb{y}$ are binary variables, in which only one is equal to $1$ and the remaining are $0$. This allows to straightforwardly compare the estimated probabilities delivered by the softmax function with the expected ones according to the value of $y$.

Relying on a labeled one-hot encoded training set of $m$ i.i.d. samples $X = \left\\{(\mathbf{x}^{(1)}, \mathbf{y}^{(1)}) \ldots, (\mathbf{x}^{(m)}, \mathbf{y}^{(m)})\right\\}$, we can find the optimal values for $\\{\mathbf{w_1}, \mathbf{w_2}, \ldots, \mathbf{w_K}\\}$ and $\\{b_1, b_2, \ldots, b_K\\}$ relying on the maximum likelihood estimation method. In particular, $\mathbf{y}$ follows a generalized Bernoulli distribution 

$$P\left(\mathbf{y}^{(i)} \;\vert\; \mathbf{x}^{(i)}\right) = \prod_{k=1}^K \left(\hat{p_k}(\mathbf{x}^{(i)}, \mathbf{w_k}, b_k)\right)^{y_k^{(i)}}$$

Defining $\Theta$ as the union of all the searched parameters to ease the notation, and the cost function $J(X, \Theta)$ as the negative version of the log-likelihood averaged over the $m$ samples of the dataset, then

$$J(X, \Theta) = -\frac{1}{m}\sum_{i = 1}^m \sum_{k=1}^K y_k^{(i)} \log\left(\hat{p_k}(\mathbf{x}^{(i)}, \mathbf{w_k}, b_k)\right)$$

which again resembles the cross-entropy function.

As with logistic regression, the cost function $J(X, \Theta)$ needs to be minimized relying on an optimization algorithm such as gradient descent.

</div>