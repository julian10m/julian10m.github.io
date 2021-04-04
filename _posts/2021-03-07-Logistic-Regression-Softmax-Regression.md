---
layout: post
title: Logistic and Softmax Regression
---

Logistic and softmax regression are supervised machine learning algorithms used for classification tasks. Given a categorical target variable $y$ that may take $K$ possible values, and an observation $\mathbf{x}$ composed of $N$ features/predictors, i.e., $\mathbf{x}=[x_1, x_2\ldots,x_N]^\intercal$, these algorithms estimate the probability a posteriori of $y$ given $\mathbf{x}$. Based on this estimation, these classifiers apply a decision rule that allows them to classify each sample, i.e., assign them an estimated class.

## Logistic Regression

This method is used in binary classification tasks, i.e., cases where $K=2$. However, by combining multiple logistic regression classifiers, it is possible to extend their use to multinomial cases, i.e., scenarios where $K>2$.  

### Binary Classification

The target variable can only take two possibles values, e.g., "yes/no", "win/lose", "apple/orange", "woman/man".  We usually use 0 and 1 to numerically represent these values, i.e., $y \in \left\\{0, 1\right\\}$.  This way, in each respective scenario, $y=1$ may represent "yes", "win", "apple" or "woman",  while $y=0$ be associated to "no", "lose", "orange" or "man". We can also assign the labels in the opposite order, this actually has no impact on the performance of logistic regression.

#### Sigmoid Function

One of the main components of logistic regression is the sigmoid function $\sigma: \mathbb{R}\to (0, 1)$, defined as 

$$\sigma(z)= \frac{e^z}{1+e^z}\label{sigmoid}$$

According to Eq. (\ref{sigmoid}), $\sigma(z)$ always lays between 0 and 1. In particular, since $e^0 = 1$, then $\sigma(0) = 1/2$. For large values of $z$, the term $e^z$ becomes a large positive number, and $\sigma(z)$ tends to $1$. On the other hand, when $z$ takes large negative values, $e^z$ approaches $0$, and thus $\sigma(z)$ is close to $0$. 

In addition, we have

$$1 - \sigma(z) = \frac{1}{1+e^z} = \sigma(-z)$$

which means that $1 - \sigma(z)$ is a mirrored copy of $\sigma(z)$.

<center>
<img src="/files/Figures/Logistic-Softmax-Regression/sigmoid.png" alt="Plot of sigmoid function">

<br>
<em>Sigmoid function.</em>
</center>

At the same time, it is trivial to show that 

$$\log\left(\frac{\sigma(z)}{1 - \sigma(z)}\right) = z \label{logitSigma}$$

Finally, we can see that

$$\sigma'(z)=\frac{\mathrm{d}\sigma(z)}{\mathrm{d}z} = \frac{e^z(1+e^z) - e^z e^z}{(1 + e^z)^2} = \frac{e^z}{1 + e^z}\cdot\frac{1}{1 + e^z}$$

$$\Longrightarrow \sigma'(z)= \sigma(z)\left(1 - \sigma(z)\right) \label{derivative}$$

Since $\sigma(z)$ and $1- \sigma(z)$ are always positive, so is $\sigma'(z)$, and thus $\sigma(z)$ is a monotonically increasing function. While the maximum of $\sigma'(z)$ is placed in $z=0$, as the module of $z$ increases, $\sigma'(z)$ approaches zero in a symmetrical way since $\sigma'(z) = \sigma'(-z)$, i.e. $\sigma'(z)$ is an even function.

<center>
<img src="/files/Figures/Logistic-Softmax-Regression/derivative_sigmoid.png" alt="Plot of derivative of sigmoid function">

<br>
<em>Derivative of sigmoid function.</em>
</center>

According to Eq. (\ref{derivative}), we can write

$$\sigma(z)= \frac{1 - \sigma(z)}{\sigma'(z)}\label{derivative1}$$

$$1 - \sigma(z) = \frac{\sigma(z)}{\sigma'(z)}\label{derivative2}$$

We will later come back to Eq. (\ref{logitSigma}), (\ref{derivative1}) and (\ref{derivative2}).

#### A model to estimate probabilities

To classify any sample $\mathbf{x}$, assuming a probabilistic model, we are interested in estimating $P(y = 1 \;\vert\; \mathbf{x})$ and $P(y = 0 \;\vert\; \mathbf{x})$. To minimize the classification error, according to Bayesian decision theory, if $P(y=1 \;\vert\; \mathbf{x}) > P(y=0 \;\vert\; \mathbf{x})$, then our guess should be $\hat{y} = 1$, and $\hat{y} = 0$ otherwise. In practice, since $P(y = 1 \;\vert\; \mathbf{x}) + P(y = 0 \;\vert\; \mathbf{x}) = 1$, then any of them can be written as a function of the other one, and thus we actually only need to estimate one of these probabilities.

Logistic regression proposes using a sigmoid function to estimate $P\left(y = 1 \;\vert\; \mathbf{x}\right)$. Indeed, the fact that the image of the sigmoid function is constrained to the interval $(0, 1)$ allows to use this function to express the probability of an event. The model is such that the independent variable $z$ takes values that depend on those of the features $\mathbf{x})$, we can write $z = f(\mathbf{x})$. In particular, logistic regression uses the following classification rule

$$\hat{y} =  
\begin{cases}
    1,& \text{if } \sigma(z) \geq 0.5\\
    0,& \text{otherwise}
\end{cases}
 $$

Since $\sigma(z) = 0.5$ only when $z = 0$, then we can re-write the last condition as 

 $$\hat{y} =  
\begin{cases}
    1,& \text{if } z \geq 0\\
    0,& \text{otherwise}
\end{cases} \label{ZDecisionBoundary}
 $$

 <center>
<img src="/files/Figures/Logistic-Softmax-Regression/decision_region_logistic_0.5.png" alt="Plot of decision regions and boundary">

<br>
<em>Decision regions and boundary.</em>
</center>

In order to be able to use our model, however, we still need to define how $z$ depends on $\mathbf{x}$, i.e. the shape of $f(\mathbf{x})$.

#### Defining parameters to adjust the estimations

For a sample $\mathbf{x}$, the input to the sigmoid function is determined as the product between $\mathbf{x}$ and a weighting vector of $N$ parameters $\mathbf{w} = [w_1, \ldots, w_N]^\intercal$ plus a a bias or intercept $b$

$$f(\mathbf{x}) = \mathbf{w}^\intercal \mathbf{x} + b $$

and hence we can write

$$\hat{p_1}(\mathbf{x}, \mathbf{w}, b)= \hat{P}\left(y = 1 \;\vert\; \mathbf{x}\right)= \left. \sigma(z) \right\rvert_{z = \mathbf{w}^\intercal \mathbf{x} + b} = \sigma(\mathbf{w}^\intercal \mathbf{x} + b)$$

In Eq. (\ref{logitSigma}), replacing $\sigma(z)$ and $z$ respectively by $\hat{p_1}(\mathbf{x}, \mathbf{w}, b)$ and $\mathbf{w}^\intercal \mathbf{x} + b$, then we have

$$\log\left(\frac{\hat{p_1}(\mathbf{x}, \mathbf{w}, b)}{1 - \hat{p_1}(\mathbf{x}, \mathbf{w}, b)}\right) = \mathbf{w}^\intercal \mathbf{x} + b \label{logitProba}$$

where the left term is known as the log-odds or logit of $\hat{p_1}(\mathbf{x}, \mathbf{w}, b)$. This means that the sigmoid function takes a logit of $\hat{p_1}(\mathbf{x}, \mathbf{w}, b)$ as input, and outputs the estimated probability $\hat{p_1}(\mathbf{x}, \mathbf{w}, b)$.

In addition, Eq. (\ref{logitProba}) resembles the one we previously saw for linear regression, where $\hat{y} =  \mathbf{w}^\intercal \mathbf{x} + b$. This gives us a hint of from where the logistic regression name comes: we are applying a regression to the logits of $\hat{p_1}(\mathbf{x}, \mathbf{w}, b)$.

#### Classification Rule and Decision Boundary

Despite the close relationship between linear and logistic regression, the first is used for regression tasks, but the latter in classification problems.
We can update the classification rule in Eq. (\ref{ZDecisionBoundary}) by

 $$\hat{y} =  
\begin{cases}
    1,& \text{if } \mathbf{w}^\intercal \mathbf{x} + b \geq 0\\
    0,& \text{otherwise}
\end{cases}
 $$

Analyzing this expression, we can see that there exists a decision boundary in $\mathbf{w}^\intercal \mathbf{x} + b = 0$. The values of $\mathbf{x}$ that satisfy this condition form an (affine) hyperplane in the sub-space generated by $\left\\{x_1, x_2, \ldots, x_n\right\\}$, where $b$ is the quantity by which this hyperplane is shifted from the origin. When $\mathbf{w}$ and $b$ are known, if $\mathbf{x}$ is such that the computation of $\mathbf{w}^\intercal \mathbf{x} + b$ is greater than $0$, then $\hat{y} = 1$, and $\hat{y} = 0$ otherwise.

#### Cross-Entropy Loss Function

The optimal values of $\mathbf{w}$ and $b$ can be estimated relying on the maximum likelihood estimation method. Given a training set $X$ of $m$ labeled i.i.d. samples, i.e. $X = \left\\{(\mathbf{x}^{(1)}, y^{(1)}), (\mathbf{x}^{(2)}, y^{(2)}), \ldots, (\mathbf{x}^{(m)}, y^{(m)})\right\\}$ , modelling $y$ as a Bernoulli variable with parameter $p(\mathbf{x})$, then the joint distribution for the dataset is

$$\begin{align}
P\left(y^{(1)}, y^{(2)}, \ldots, y^{(m)} \;\vert\; \mathbf{x}^{(1)}, \mathbf{x}^{(2)}, \ldots, \mathbf{x}^{(m)}\right) &= \prod_{i=1}^m P\left(y^{(i)}\;\vert\;\ \mathbf{x}^{(i)}\right) \\
&= \prod_{i=1}^m \left(p(\mathbf{x}^{(i)})\right)^{y^{(i)}}\cdot\left(1 - p(\mathbf{x}^{(i)})\right)^{(1-y^{(i)})}
\end{align}
$$

where $p(\mathbf{x}^{(i)})$ is the probability that sample $\mathbf{x}^{(i)}$ might belong to class $1$ and $y^{(i)}$ is the class to which $\mathbf{x}^{(i)}$ actually belongs to.

The log-likelihood, that we seek to maximize, can then be written as

$$l(X, \mathbf{w}, b) = \sum_{i=1}^m \left(y^{(i)} \log \left(p(\mathbf{x}^{(i)})\right) + (1 - y^{(i)})\log\left(1 - p(\mathbf{x}^{(i)})\right)\right)$$

Recalling we estimate $p(\mathbf{x}^{(i)})$ as $\hat{p}\_1(\mathbf{x}^{(i)}, \mathbf{w}, b) = \sigma(\mathbf{w}^\intercal \mathbf{x} + b)$, and defining the cost function $J(X, \mathbf{w}, b)$ as the negative version of the log-likelihood averaged over the $m$ samples composing the dataset, then

$$
J(X, \mathbf{w}, b) =  -\frac{1}{m}\sum_{i = 1}^m \left(y^{(i)} \log\left(\sigma(\mathbf{w}^\intercal \mathbf{x} + b)\right)+ (1 - y^{(i)}) \log\left(1 - \sigma(\mathbf{w}^\intercal \mathbf{x} + b)\right)\right) \label{cost-function}
$$

Since this mathematical expression resembles the one used in information theory to obtain the "cross-entropy" between two probability distributions, then this cost function is usually referred to as the **cross-entropy loss function**. An important, and convenient, characteristic of this function is that it is convex, i.e., it has a unique minimum, which is thus an absolute minimum of the function.

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

 <center>
<img src="/files/Figures/Logistic-Softmax-Regression/cost_function_logistic.png" alt="Plot of cost function when y = 1 and y = 0">

<br>
<em>Cost function composition.</em>
</center>

For example, when $y^{(i)} = 1$, if $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) = 1$, then the cost contributed by sample $(\mathbf{x}^{(i)}, y^{(i)})$ is 0. However, on the opposite extreme, if $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) = 0$, then the cost would become infinite. For intermediate values, the logarithm in the expression leads to penalize more the "worse" decisions, e.g., for $y^{(i)} = 1$, the smaller $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)$ is, the much larger $\log\left(\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right)$ will be.  In addition, analyzing the expression, we see that the cost for $y^{(i)} = 0$ is a shifted mirrored copy of that for $y^{(i)} = 1$: the curve is identical, but rather penalizing more severely the more that $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)$ is closer to $1$.

Finally, it must be noted that, even if our classifier would have made correct classifications, e.g. estimated $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) > 0.5$ in a case where $y^{(i)} = 1$, we are still increasing the cost function in a quantity $c\left( y^{(i)}, \hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b)\right)$. Indeed, the cost function does not penalize classification mistakes, but more generally the fact that the classifier makes "doubtful" classifications. 
In other words, for all samples $\mathbf{x}^{(i)}$ and $\mathbf{x}^{(j)}$ for which $y^{(i)}=1$ and $y^{(j)}=0$, we would want our classifier not to hesitate, i.e., to end up computing $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) = 1$ and $\hat{p_1}(\mathbf{x}^{(i)}, \mathbf{w}, b) = 0$, respectively.
This means that for all samples where the classifier was not completely "convinced" or "sure" of its choice, we should increase $J(X, \mathbf{w}, b)$.
In particular, minimizing the cross-entropy loss function tends to do this, thus generating estimations that resemble as closely as possible the real values of $y$ for all samples.

#### Optimizing the parameters 

To find the optimal value of $\mathbf{w}$ and $b$, we need to minimize the cost function $J(X, \mathbf{w}, b)$. Analyzing Eq. (\ref{cost-function}), we can see that $J(X, \mathbf{w}, b)$ is differentiable, since it is a composition of differentiable functions. Hence, we can find the minimum of $J(X, \mathbf{w}, b)$ looking for the point where all the partial derivatives become null. 

To begin, we can first focus on the derivative of $J(X, \mathbf{w}, b)$ with respect to a component $w_j$ of $\mathbf{w}$. Noting that in Eq. (\ref{cost-function}), $y^{(i)}$ does not depend on $w_j$, then we only need to find the derivatives of $\log\left(\left. \sigma(z) \right\rvert_{z = \mathbf{w}^\intercal \mathbf{x} + b}\right)$ and $\log\left(1 - \left. \sigma(z) \right\rvert_{z = \mathbf{w}^\intercal \mathbf{x} + b}\right)$. 

To derive these calculations, first note that

$$\frac{\partial J(X, \mathbf{w}, b)}{\partial w_j} = \frac{\partial J(X, \mathbf{w}, b)}{\partial z} \frac{\partial z}{\partial w_j}$$

where it is trivial to see that $\frac{\partial z}{\partial w_j} = x_j^{(i)}$, and hence we only need to find $\frac{\partial J(X, \mathbf{w}, b)}{\partial z}$.

Since 

$$\frac{\partial \log(\sigma(z))}{\partial z} = \frac{\sigma'(z)}{\sigma(z)}$$

$$\frac{\partial \log(1 - \sigma(z))}{\partial z} = -\frac{\sigma'(z)}{1 - \sigma(z)}$$

we can use the results of Eq. (\ref{derivative1}) and (\ref{derivative2}) to solve each of these calculations.

Applying some algebra, it is simple to show that

$$\frac{\partial J(X, \mathbf{w}, b)}{\partial w_j} = \frac{1}{m} \sum_{i=1}^m x_j^{(i)}\left(\sigma(\mathbf{w}^\intercal \mathbf{x}^{(i)} + b) - y^{(i)}\right) \label{dJdw-LR}
$$

Proceeding in a similar way, considering that $\frac{\partial z}{\partial b} = 1$, we can also find that

$$\frac{\partial J(X, \mathbf{w}, b)}{\partial b} = \frac{1}{m} \sum_{i=1}^m \left(\sigma(\mathbf{w}^\intercal \mathbf{x}^{(i)} + b) - y^{(i)}\right) \label{dJdb-LR}
$$

Unfortunately, setting Eq. (\ref{dJdw-LR}) and (\ref{dJdb-LR}) to zero results into what are called transcendental equations, i.e., equations for which there is no closed form to solve them. To overcome this limitation, the only option left is to rely on numerical solutions, e.g. the gradient descent algorithm.


#### Gradient Descent Algorithm 

When applying the gradient descent algorithm in a logistic regression problem, note that since $J(X, \mathbf{w}, b)$ is convex, converging towards the only, and thus optimal, minimum is ensured. During the training, the values of $\mathbf{w}$ and $b$ will be iteratively updated, hence modifying the positioning of the hyperplane that serves as decision boundary for the classification problem. Hopefully, at the end of the process, the hyperplane will end up "well" located, i.e., for most training samples, the estimations and classifications the algorithm performs will be reasonably good. 

To implement the gradient descent algorithm, it is convenient to have a vectorized version of the functions we have studied. Defining 

$$\mathbf{X}^\intercal = \begin{bmatrix}
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

$$J(\mathbf{X}, \Theta) = -\frac{1}{m}\Big(\mathbf{y}^\intercal \log\left(\sigma(\mathbf{X} \Theta)\right)+ (1 - \mathbf{y})^\intercal \log\left(1 - \sigma(\mathbf{X} \Theta)\right)\Big)$$

$$\nabla J(\mathbf{X}, \Theta) = \frac{1}{m} X^\intercal\left(\sigma(\mathbf{X} \Theta) - \mathbf{y}\right)$$

Note that the gradient descent algorithm represents only one option out of the multiple optimization algorithms that can be used to obtain a numerical solution.

###  Multinomial Classification

A logistic regression classifier only works for binary classification problems. When $y$ may take more than two values, i.e., $K>2$, however, we can still use logistic regression by relying in multiple classifiers of this type. More specifically, the proposal in these cases is to divide and conquer: instead of trying to come up with an algorithm that directly assigns the correct label to a sample $\mathbf{x}$, the results of multiple logistic regression classifiers that solve simpler binary classification problems are aggregated and used to reach a conclusion. 

In particular, two different approaches exist to combine multiple classifiers: one-vs-the-rest (OvR), also called one-vs-all (OvA), and one-vs-one (OvO). In the following, we assume that the training dataset $X$ of $m$ i.i.d. labeled samples contains, $m_k$ samples for each class $k$,

$$m_k = \sum_{i = 1}^m \left[y^{(i)} = k\right]$$

such that $m = \sum_{k=1}^K m_k$.

#### OvR/OvA

The multinomial classification problem is divided in $K$ binary estimation problems, and then one decision rule is applied. 

Each logistic regression classifier focuses on one specific label, estimating the chances that this might be the correct one for the sample being analyzed. To determine this, for any training sample $(\mathbf{x}^{(i)}, y^{(i)})$, the estimator related to label $k$ re-labels the samples in a variable $z$ such that

$$z^{(i)} =  
\begin{cases}
    1,& \text{if } y^{(i)} = k\\
    0,& \text{otherwise}
\end{cases}
 $$

While the $m_k$ samples of class $k$ remain unaltered, the $m - m_k$ samples belonging to the remaining $K-1$ labels are aggregated into a unique class. 

Each classifier is then trained on each of these re-labeled datasets.
In particular, the probability $P(z^{(i)} = 1 \;\vert\; \mathbf{x}^{(i)})$ for the $k^{th}$estimator is not other than $P(y^{(i)} = k \;\vert\; \mathbf{x})$.


To classify a new sample $\mathbf{x}^{(i)}$, first we compute the $K$ estimations and then we assign $\mathbf{x}^{(i)}$ the label associated with the estimator that outputs the greatest value, e.g. label $k$ if the $k^{th}$ estimator outputs the largest value.

#### OvO

The multinomial classification task is divided in multiple binary classification problems, and the decision is reached by majority voting. 

Each logistic regression classifier compares two classes $k_1$ and $k_2$ at a time, as if the remaining did not exist. The following re-labeling is applied

$$z^{(i)} = 
\begin{cases}
    1, & \text{if } y^{(i)} = k_1 \\
    0, & \text{if } y^{(i)} = k_2 \\
    \text{Discard}, & \text{otherwise}
\end{cases}
 $$

where we see that we only keep the $m_{k_1}$ and $m_{k_2}$ samples of class $k_1$ and $k_2$, identifying one with label $z^{(i)} = 1$ and the other one with $z^{(i)} = 0$.

In total, for a classification problem with $K$ labels, we need ${k \choose 2} = \frac{K(K-1)}{2}$ logistic regression classifiers.
This number represents the possible ways of combining two labels. The reasoning is as follows. If there are $K$ labels, then we can link each to the remaining $K-1$ labels. But if we count all the possible ways of linking labels for each label, then the $K(K-1)$ combinations are actually counting every linking possibility twice, so we need to divide by 2. Therefore, the number of classifiers needed is $\frac{K(K-1)}{2}$, as previously mentioned.

When classifying a new sample $\mathbf{x}^{(i)}$, first we need to perform the $K(K-1)/2$ classifications, and ultimately assign $\mathbf{x}^{(i)}$ to the class that results most voted across all classifiers.

## Softmax Regression

 While workarounds such as OvR/OvA and OvO allow to use logistic regression for multinomial classification tasks, softmax regression is a method specifically conceived for this purpose. In particular, softmax regression takes $K$ weighting vectors $\\{\mathbf{w_1}, \mathbf{w_2}, \ldots, \mathbf{w_K}\\}$ and $K$ intercepts $\\{b_1, b_2, \ldots, b_K\\}$ as parameters. To estimate one probability per class, different to the previous methods, we tune the complete set of parameters jointly.

### Softmax Function

To apply softmax regression, we generalize the sigmoid function $\sigma(z)$ used in logistic regression with the softmax function, i.e. with a 
vector function $\Psi: \mathbb{R}^K \to (0, 1)^K$ defined as 

$$\Psi(z_1, z_2, \ldots, z_K) = \begin{bmatrix}
\psi_1(z_1, z_2, \ldots, z_K) \\
\psi_2(z_1, z_2, \ldots, z_K) \\
\vdots \\
\psi_K(z_1, z_2, \ldots, z_K) \\
\end{bmatrix}$$

where each $\psi_j:  \mathbb{R}^K \to (0, 1)$ is a scalar function 

$$\psi_k(z_1, z_2, \ldots, z_K) = \frac{e^{z_k}}{\sum_{l=1}^K e^{z_l}}$$ 

According to their definition, we can see that 

- $0 <  \psi_k(z_1, z_2, \ldots, z_K) < 1$ for all components, and;
- $\sum_{k=1}^K \psi_k = 1$

In addition, note that

$$\Psi(0, z) = \begin{bmatrix}
\frac{1}{1 + e^z} \\[1.5ex]
\frac{e^z}{1 + e^z} 
\end{bmatrix}$$

such that the first component equals $1 - \sigma(z)$ and the remaining one $\sigma(z)$.


The name softmax function for $\Psi(z_1, z_2, \ldots, z_K)$ comes from the fact that this function is related to the $\operatorname{argmax}(z_1, z_2, \ldots, z_K)$ and $\operatorname{max}(z_1, z_2, \ldots, z_K)$ functions. While the first returns the index $j$ for which $z_j$ is maximum, the second one returns $z_j$ itself. 

A smooth approximation to the $\operatorname{max}$ function is the LogSumExp function 

$$\operatorname{LSE}(z_1, z_2, \ldots, z_K) = \log\left(\sum_{k=1}^K e^{z_k}\right)$$

 For example, $\operatorname{max}(0, z)$ can be approximated with $\operatorname{LSE}(0, z) = \log\left(1 + e^z\right)$.

Taking the derivative of $\operatorname{LSE}(z_1, z_2, \ldots, z_K)$ with respect to $z_l$, it is trivial to see that

$$\frac{\partial\operatorname{LSE}(z_1, z_2, \ldots, z_K)}{\partial z_l} = 
 \psi_l(z_1, z_2, \ldots, z_K)$$

and that then $\Psi(z_1, z_2, \ldots, z_K)$ is the gradient of $\operatorname{LSE}(z_1, z_2, \ldots, z_K)$.

On the other hand, note that $\sum_{l=1}^K e^{z_l}$ can be roughly approximated by $\max_j e^{z_j}$. The $\operatorname{argmax}$ function would enforce the component $j$ to be equal to $1$, and the remaining to $0$. On the other hand, the softmax function will produce an output such that $\forall k \neq j$, the value of $\psi_k(z_1, z_2, \dots, z_K)$ will tend to be low, but not exactly $0$,  and at index $j$,  $\psi_j(z_1, z_2, \dots, z_K)$ will tend to be higher, though not exactly $1$.
Hence, in this sense, the softmax function smoothly approaches  the $\operatorname{argmax}$ function. The advantage of using this approximation is that the softmax function avoids the singular points that the $\operatorname{argmax}$ function includes. This is particularly beneficial in optimization tasks, since it allows to avoid both non-converging and diverging solutions.

#### Partial derivatives

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

### Estimating the probability distribution

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

and express the same as $\left. \psi_k(\mathbf{z})\right\rvert_{\mathbf{z} = \mathbf{W}^\intercal \mathbf{x} + \mathbf{b}}$.

The objective of softmax regression then becomes correctly tuning the $K$ weighting vectors and $K$ intercepts composing $\mathbf{W}$ and $\mathbf{b}$, respectively, 
in order to achieve a "good" classification performance.

Finally, rather than taking a value from $1$ to $K$, we represent the output variable as a vector $\mathbf{y} \in \mathbb{R}^{K \times 1}$

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

### Generalizing the cross-entropy loss function

Relying on a labeled one-hot encoded training set of $m$ i.i.d. samples $X = \left\\{(\mathbf{x}^{(1)}, \mathbf{y}^{(1)}), \mathbf{x}^{(2)}, \mathbf{y}^{(2)}), \ldots, (\mathbf{x}^{(m)}, \mathbf{y}^{(m)})\right\\}$, we can find the optimal values for $\\{\mathbf{w_1}, \mathbf{w_2}, \ldots, \mathbf{w_K}\\}$ and $\\{b_1, b_2, \ldots, b_K\\}$ relying on the maximum likelihood estimation method. In particular, each $\mathbf{y}^{(i)}$ follows a generalized Bernoulli distribution 

$$P\left(\mathbf{y}^{(i)} \;\vert\; \mathbf{x}^{(i)}\right) = \prod_{k=1}^K \left(p_k(\mathbf{x}^{(i)})\right)^{y_k^{(i)}}$$

Recalling that we are estimating $p_k(\mathbf{x}^{(i)})$ as $\hat{p_k}(\mathbf{x}^{(i)}, \mathbf{W}, \mathbf{b}) = \left. \psi_k(\mathbf{z})\right\rvert_{\mathbf{z} = \mathbf{W}^\intercal \mathbf{x} + \mathbf{b}}$,  and considering the cost function $J(X, \mathbf{W}, \mathbf{b})$ as the negative version of the log-likelihood averaged over the $m$ samples of the dataset, then we have

$$J(X, \mathbf{W}, \mathbf{b}) = -\frac{1}{m}\sum_{i = 1}^m \sum_{k=1}^K y_k^{(i)} \log\left(\left. \psi_k(\mathbf{z})\right\rvert_{\mathbf{z} = \mathbf{W}^\intercal \mathbf{x} + \mathbf{b}}\right)$$

which again resembles the cross-entropy function, this time between $\mathbf{y}$ and $\left. \Psi(\mathbf{z})\right\rvert_{\mathbf{z} = \mathbf{W}^\intercal \mathbf{x} + \mathbf{b}}$.

As we previously saw with logistic regression, this

the cost function $J(X, \mathbf{W}, \mathbf{b})$ needs to be minimized relying on an optimization algorithm such as gradient descent.