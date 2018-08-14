---
layout: post
title: "Learning Rate Schedules for Gradient Descent"
tags: Optimization
use_math: true
---

* TOC
{:toc}

## Introduction

Stochastic gradient descent and random reshuffling help overcome the weakness of batch gradient descent in dealing with local minima and saddle points at non-convex functions by increasing the variability of the loss surface by approximating it with a smaller batch of data. The cost of the extra variability of these methods is slow convergence around local minima, as the estimate 'jumps' around. In order to deal with this challenge one often uses a learning rate schedule which decreases, or anneals, the learning rate parameter over time.

There are many choices on how to anneal the learning rate, but generally they can be separated across two choices; when do we choose to anneal the learning rate, and what form does the annealing take. The easiest to implement method for choosing when to decrease the learning rate is to make it a function of the current epoch. A second method that is more computationally demanding but helps prevent from overfitting is to decrease the learning rate whenever the validation error increases.

A cost of annealing methods is the additional complexity they carry, as almost all forms require the specification of additional hyperparameters, increases the challenge of the later hyperparemeter optimization.

## Methods

In this section I am going to describe some of the more common annealing methods, but this is no way an exhaustive list. The annealing schedule for these examples is based on epoch instead of validation error for ease of interpretation. There are two plots for each method displaying the general form of the learning rate over time, as well as an example of how the algorithm moves on a simulated linear regression function. All of these examples are done using Random Reshuffling.

### Constant

A constant learning rate is the simplest form of learning rate schedule, and as we can see here it does a good job of approaching the minimum, but struggles to converge.

$$
\alpha(t) = \alpha_0
$$

<img src="/images/posts/LearningRate/LR_constant.png" width="100%" style="padding:0px"/>

### Exponential Decay

The addition of an exponential term and a new decay hyperparameter allows the learning rate to decrease exponentially, helping it stabilize after a certain number of iterations assuming it has reached the region around the minimum.

$$
\alpha(t) = \alpha_0 e^{-kt}
$$

<img src="/images/posts/LearningRate/LR_exp.png" width="100%" style="padding:0px"/>

### Time Based Decay

The time based decay schedule is extremely similar to the exponential decay schedule, but specified a little differently.

$$
\alpha(t) = \alpha_0 \frac{1}{1+dt} \ : d=\text{decay rate}
$$

<img src="/images/posts/LearningRate/LR_timedecay.png" width="100%" style="padding:0px"/>

### Step Decay

The step decay schedule adds in a third hyperparameter alongside $\alpha_0$ and $d$, which is the number of iterations $T$ required for a step change.

$$
\alpha(t) = \alpha_0 d^{floor \frac{t}{T}} \ : T = \text{Number of iterations for step change}
$$

<img src="/images/posts/LearningRate/LR_step.png" width="100%" style="padding:0px"/>

### Cyclical

One of the weaknesses of the methods above is that they can also get stuck in local minima when the learning rate has become very small. In order to address this problem, while still helping convergence through a decreasing learning rate, the cyclical methods were created. The cyclical nature of the learning rate schedule allows escape from local minima by re-increasing the learning rate every set number of epoch. These methods can follow many different forms depending on the analysts problem, and three such forms are mentioned here.

The primarily hyperparameters for the cyclical methods are $\alpha_{in}$ (the minimum learning rate), $\alpha_{max}$ (the maximum learning rate), the current cycle $(C)$, and the cycle length $(L)$

#### Constant

The simplest form keeps $\alpha_{max}$ constant across cycles. We can see on the linear regression that the change in step size across epochs is varying as we traverse across the cycles.

$$
\begin{align*}
\alpha(t) & = \alpha_{min} +(\alpha_{min}-\alpha_{max}) \cdot max(0,1-x) \\
x & = \left|\frac{t}{L}-2C+1\right| \\
C & = floor\left(\frac{1+t}{2C}\right) \\
\end{align*}
$$

<img src="/images/posts/LearningRate/LR_cycle_constant.png" width="100%" style="padding:0px"/>

#### Step

The cyclical methods can incorporate concepts mentioned in earlier learning rate schedules, such as step based decay. This works by decreasing $\alpha_{max}$ as the cycle number $(C)$ increases.

$$
\alpha(t) = \alpha_{min} +(\alpha_{min}-\alpha_{max}) \cdot max(0,1-x) \cdot \frac{1}{2^{C-1}} \\
$$

<img src="/images/posts/LearningRate/LR_cycle_step.png" width="100%" style="padding:0px"/>

#### Exponential

$\alpha_{max}$ can also be annealed by including an exponential term and a new hyperparameter $\gamma \in (0,1)$.

$$
\alpha(t) = \alpha_{min} +(\alpha_{min}-\alpha_{max}) \cdot max(0,1-x) \cdot \gamma^C
$$

<img src="/images/posts/LearningRate/LR_cycle_exp.png" width="100%" style="padding:0px"/>

### Warm Restart

The warm restart method is essentially an alteration of the cyclical method which uses the cosine function instead of the floor function to create cycles. One benefit of this method is that the restart jumps directly from $\alpha_{min}$ to $\alpha_{max}$ rather than having to reclimb back up the hill.

This method can incorporate step and exponential decay as well, just as with the cyclical functions. Here just the constant form is shown.

$$
\alpha(t) = \alpha_{min} + \frac{1}{2}(\alpha_{max}-\alpha_{min}) \cdot \left(1+cos\left(1+\pi \frac{I_t}{L}\right)\right)
$$

<img src="/images/posts/LearningRate/LR_warm.png" width="100%" style="padding:0px"/>
