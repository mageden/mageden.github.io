---
layout: post
title: "Stochastic/Mini-batch Gradient Descent"
tags: Optimization
use_math: true
---

* TOC
{:toc}

<script type='text/javascript' src='http://ajax.googleapis.com/ajax/libs/jquery/1.4.1/jquery.min.js'></script>
<script type='text/javascript' src='javascript/touchHover.js'></script>

## Introduction

The [previous post](\2018\07\24\BatchGD.html) introduced batch gradient descent, in which the entire sample size ($N$) is used for each iteration of the algorithm. This method is widely used due to its great convergence towards local minima and the limited number of hyperparameters involved (learning rate and starting value). Batch gradient descent suffers from a number of limitations that often come up when dealing with real world problems;

1. Batch requires the whole training set to be held in memory, which can be extremely slow or impossible for large data sets.
2. Adding new observations requires the whole algorithm to be rerun in batch, rather than updating the previous model starting with just the new information.
3. Batch can get stuck in local optimum and saddle points easily, making it less ideal for non-convex problems.
4. Using the entire dataset for each iteration rather than a subset can slow down convergence as calculating the cost function across the entire dataset can considerably slow down calculations, and only a few observations may be required for a reasonable estimate.

Many of these limitations can be addressed through changing the number of samples used per iteration $(n_{iter})$ to be less than the total number of observations $(N)$. There are three general forms when $n_{iter}=1$; **deterministic incremental gradient descent** (IG) samples without replacement in the same order across the dataset, **random reshuffling** (RR)samples without replacement in a randomized order, and **stochastic gradient descent** (SGD) samples with replacement. It is not atypical for these three methods to all be called stochastic gradient descent, so attention must be paid to their implementation. The other most common method is **mini-batch gradient descent** (MBGD) where $1<n_{iter}<N$ and the observations are typically sampled without replacement in a randomized order.  

By lowering $n_{\text{iter}}<N$ we can decrease the memory burden of the computation, requiring a smaller portion of the data to be held in memory at one time (addressing limitations 1 & 4). This also increases the ease through which newer observations can be added by beginning the algorithm on the new data, rather than the total data set (limitation 2). Most importantly, the cost surface for many problems is not convex, making batch gradient descent problematic as it can get stuck in local minimum/saddle points and is consequently particularly sensitive to the starting values. The loss surface changes across iterations as the observations used for training change. This increases the variability of the algorithm and allows it to escape local minima and saddle points more easily (limitation 3; Dauphin et al., ). Since the loss surface is noisier across iterations, both stochastic and mini-batch gradient descent usually take more iterations to converge than batch gradient descent, but each iterations is quicker to calculate.  

The differences between the intuition of <abbr title="Stochastic Gradient Descent">SGD</abbr>/<abbr title="Mini-batch Gradient Descent">MBGD</abbr> are relatively simple and intuitive, requiring only a few changes when implementing. The most obvious is the selection of the batch size to be used. Additionally, one needs to consider the sampling process for each batch and how to adjust the learning rate in order to deal with the increased volatility of the algorithm.

### Sampling Observations

The sampling properties of observations within the algorithm become relevant for <abbr title="Stochastic Gradient Descent">SGD</abbr> and <abbr title="Mini-batch Gradient Descent">MBGD</abbr>, since unlike batch gradient descent they do not use the entire set of observations. The two considerations are whether to sample with vs. without replacement and whether sampling should occur in a random or fixed pattern. Sampling without replacement is usually preferred over sampling with replacement because it is computationally easier, it guarantees all observations are used providing the most information possible, and has better convergence. These properties are quite challenging to prove, but the interested reader can consult Shamir (2016) for the technical details.

Once we have decided to sample without replacement, the question is whether the data should be sampled in a random (<abbr title="Random Reshuffling">RR</abbr>) or fixed order (<abbr title="Deterministic Incremental Gradient Descent">IG</abbr>). A number of papers show that <abbr title="Random Reshuffling">RR</abbr> tends to beat out <abbr title="Deterministic Incremental Gradient Descent">IG</abbr> and <abbr title="Stochastic Gradient Descent">SGD</abbr> in most practical situations (Gurbuzbalaban et al., 2015; Shamir, 2016; HaoChen & Sra, 2018; Ying et al., 2018). One of the reasons that order of the observations matters is that there may be multiple observations in a row that are very similar casuing the algorithm to temporarily get stuck in local minima, whereas a different observation may help the algorithm escape. <abbr title="Mini-batch Gradient Descent">MBGD</abbr> is more influenced by the choice of sampling than <abbr title="Stochastic Gradient Descent">SGD</abbr>, as in addition to the order of the data we must consider the characteristics of the mini-batches. A fixed order results in less variability of the loss surface across iterations, as there are a fixed number of surfaces, rather than if the observations were sampled randomly. The other issue with a fixed order is that we become more sensitive to 'bad batches', which may not be representative of the overall data and could lead the estimates further from the optimum.

### Learning Rate

The selection of the learning rate is always critical for gradient descent; if it is too large the algorithm may overshoot good solutions and potentially undo progress from previous iterations, but when too small and it takes more iterations to travel what could have been done in fewer. Larger learning rates can be used in <abbr title="Stochastic Gradient Descent">SGD</abbr>, <abbr title="Random Reshuffling">RR</abbr>, and <abbr title="Mini-batch Gradient Descent">MBGD</abbr> compared to batch gradient descent resulting in faster convergence (Wilson & Martinez, 2003). The addition stochasticity does come at the cost of slower convergence when near the minimum; this can be combated by lowering the learning rate over time using a learning rate schedule. This process if called annealing as we are tempering the variability of our algorithm. The simplest example would be $\lambda_{i} = \frac{\lambda_{i-1}}{1+i}$. Some of these methods will be described in more detail in a later post.

## Mini-batch Implementation

<abbr title="Mini-batch Gradient Descent">MBGD</abbr> is often preferred in practice over <abbr title="Stochastic Gradient Descent">SGD</abbr> and <abbr title="Random Reshuffling">RR</abbr> as it typically converges more quickly, is faster to compute since vectorized operations can be used, and it more easily allows for parallelization. <abbr title="Mini-batch Gradient Descent">MBGD</abbr> requires two additional choices compared to <abbr title="Stochastic Gradient Descent">SGD</abbr> and <abbr title="Random Reshuffling">RR</abbr>; what should the batch size be and how to handle samples in which $N$ is not a multiple of $n_{iter}$.

### Selecting the Batch Size

The selection of the batch size influences the variability and computational cost for each iteration. A general rule of thumb that works well in practice is used is $1<n_{iter}<32$, but I don't believe that this has any theoretical backing.

### Batch-Size Modulus

Often the total sample size $N$ is not a multiple of the batch size $n_{iter}$ resulting in the final batch of samples drawn without replacement $n_{small}$ being smaller than $n_{iter}$. As an example, imagine we have a dataset where $N=100$ and we select $n_{iter}= 9$; batch 1 will have $n_{\text{batch 1}}=9$ and $n_{\text{batch 2}}=1$. The smaller batch should adjust for the different sample size by altering the learning rate, as otherwise the observations will be weighted a little more in each final batch compared to the earlier ones.

$$
\begin{align*}
\theta_i & = \theta_{i-1} - \lambda \triangledown f(\theta_i, x_{\text{batch 1}}) : \text{Batch 1}\\
\theta_i & = \theta_{i-1} - \frac{n_{\text{batch 2}}}{n_{iter}} \lambda \triangledown f(\theta_i , x_{\text{batch 2}}) : \text{Batch 2} \\
& = \theta_{i-1} - \frac{1}{9} \lambda \triangledown f(\theta_i , x_{\text{batch 2}})
\end{align*}
$$

## References

- Dauphin, Y. N., Pascanu, R., Gulcehre, C., Cho, K., Ganguli, S., & Bengio, Y. (2014). Identifying and attacking the saddle point problem in high-dimensional non-convex optimization. *Advances in neural information processing systems*, pp. 2933-2941.
- Gurbuzbalaban M., Ozdaglar†, A., & Parrilo P. (2015). Why Random Reshuffling Beats Stochastic Gradient Descent. *arXiv preprint arXiv:1510.08560*.
- HaoChen, J. Z., & Sra, S. (2018). Random Shuffling Beats SGD after Finite Epochs. *arXiv preprint arXiv:1806.10077*.
- Shamir, O. (2016). Without-replacement sampling for stochastic gradient methods. *In Advances in Neural Information Processing Systems*, pp. 46-54.
- Wilson, D. R., & Martinez, T. R. (2003). The general inefficiency of batch training for gradient descent learning. *Neural Networks*, 16(10), 1429-1451.
- Ying, B., Yuan, K., Vlaski, S., & Sayed, A. H. (2018). Stochastic Learning under Random Reshuffling. *arXiv preprint arXiv:1803.07964*.
