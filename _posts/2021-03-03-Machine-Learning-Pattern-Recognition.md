---
layout: post
title: Machine Learning and Pattern Recognition
---

As human beings, we like understanding what surrounds us, either for the simple sake of knowing or because this gives us predictability.
In that sense, in many situations it arrives that we have a problem that we want to model, e.g., from a phenomenon that depends only on basic physics principles to others that may be influenced by human intervention or rely on the use of machines.

To build a model, the first step is to understand which variables may be of interest for the problem being analyzed.
Once these are identified, we can design experiments to test what happens when we vary the values of such variables.
This allows us to obtain some experience, i.e., to understand better the mechanics that govern the relationship among the variables. 
While we perform these studies, we may build a dataset in which we annotate what we observe in each of these experiments.
Our hope is that, by closely studying this dataset, we will be able to extract useful insights to build a representative model.
In particular, the model may have two different objectives: 

  - either simply finding whether there is some structure in the collected data, or;
  - additionally using this knowledge to make predictions, i.e., to be able to tell the expected outcomes for future experiments.

For both objectives, the idea of finding patterns in the data we analyze stands out.
We may try to find these patterns on our own, and manually generate rules that describe how our model should respond for each of them.
Another possibility is to rely on Machine Learning. 

Machine Learning is all about building models relying on algorithms that automate the pattern recognition steps.
In other words, with Machine Learning, a computer program learns patterns from data and generates a model based on this.
This is an iterative learning process, usually called training, where the algorithm asses how well the current model performs, according to a measure metric that needs to be defined in advance, and continuously adapts the learned patterns to attain a better performance. 
The performance measure metric is usually selected depending on the specific objective of the model that is being built.
The idea is that at the end of the training, leveraging having been able to extract the underlying relationships among the variables in the dataset, the resulting model will be the one that maximizes the performance. In more scientific terms, we can say that:

_"A computer program is said to learn from experience $E$ with respect to some class of tasks $T$ and performance measure $P$, if its performance at tasks in $T$, as measured by $P$, improves with experience $E$."_ -- Machine Learning, Tom Mitchell, McGraw Hill, 1997. 

Once the training finishes, we can test the model in new experiments, and based on the results, decide whether the solution is good enough, or not.
In the latter case, we can try with different Machine Learning algorithms, seeking the one that produces best results.