---
layout: post
title: "Momentum in Gradient Descent"
tags: Optimization
use_math: true
---

* TOC
{:toc}

<script type='text/javascript' src='http://ajax.googleapis.com/ajax/libs/jquery/1.4.1/jquery.min.js'></script>
<script type='text/javascript' src='/javascript/touchHover.js'></script>

## Introduction

One of the areas that gradient descent can struggle is when navigating through valleys where the gradients are small forcing the algorithm to take small steps along the loss surface. The concept of momentum can be added in order to both speed up when continuing in the same direction or to slow down when changing direction. This is particularly useful for <abbr title="Random Reshuffling">RR</abbr> and <abbr title="Stochastic Gradient Descent">SGD</abbr> where the variability of the loss surface may cause a quick change of direction. Since a change of direction can be interpreted as the algorithm having overshot a local depression, slowing down the change in direction can help reduce oscillations and converge more easily.

There are two primary forms of momentum that are often used, but both rely upon the concept of using information from the prior epoch in addition to the gradient at the current epoch.

$$
\\
\text{Reminder: Standard Gradient Descent} \\
\theta_{i} = \theta_{i-1} - \alpha \frac{\partial}{\partial \theta}F(\theta_{i-1})
$$

## Momentum

Standard momentum adds in one more hyperparameter $(m \in (0,1))$ which controls the amount of information used from the previous epoch. The algorithm is initialized with $v_0=0$ after which it is updated after each epoch.

$$
\begin{align*}
\theta_i & = \theta_{i-1} - \alpha v_{i}\\
v_i & = mv_{i-1} + \frac{\partial}{\partial \theta} F(\theta_{i-1})\\
\end{align*}
$$

When $m=0$ this simplifies to the typical form of gradient descent. It is common to have $m \in (.9, .999)$, but it is also another hyperparameter that can be optimized during hyperparameter optimization. Just like learning rate schedules for the learning rate, we can apply annealing to momentum in order to have it asymptotically approach 1 or 0.99. Too high of a momentum at the beginning of the algorithm will cause the algorithm to diverge as the gradient is already large; however, we want a larger momentum as the number of iterations increases as we expect the gradient to get smaller the closer we are to a local minima.

<img src="/images/posts/Momentum/diagrams.png" width="100%" style="padding:0px"/>

The choice of momentum is crucial as with any other hyperparameter. Too small and it will make little difference. Too large and it will have trouble converging, similar to when $\alpha$ is too large as the algorithm with boundless enthusiasm.

### Example A: Accelerating in the same direction

Here is a quick example of how momentum can accelerate gradient descent when traveling in a consistent direction. Here the cost function is $F(x) = x^2$ with $x_0 = 1$, $\alpha = .01$, $m = .2$.

$$
\begin{align*}
& \text{No Momentum} && \text{Momentum}  \\
& x_i = x_{i-1} - \alpha \triangledown F(x_{i-1}) & & x_i = x_{i-1} - \alpha \left[ mv_{i-1}+\triangledown F(x_{i-1}) \right] \\
& \ \ \ = x_{i-1} - .2(2\cdot x_{i-1}) & & \ \ \ = x_{i-1}-.2\left[.9v_{i-1}+ 2 \cdot x_{i-1} \right] \\
& x_0 = 1   & & x_0  = 1  \\
& x_1 = 1-.01(2 \cdot 1) = .98 & & x_1 = 1 - .01\left[0.9 \cdot 0 + 2 \cdot 1 \right] = .98  \\
& x_2 = .98-.01(2 \cdot .98) = .96 & & x_2 = .98 - .2\left[0.9 \cdot 2 + 2 \cdot .98 \right]=.942 \\
& \dots && \dots \\
\end{align*}
$$

### Example B: Slowing down when changing direction  

This is a second example of how momentum works for the simple quadratic function $F(x)=x^2$, this time showing how momentum slows down steps taken in the opposite direction of the previous epoch. The hyperparemeters are $x_0 = 10$, $\alpha = .7$, and $m = .2$.

$$
\begin{align*}
& \text{No Momentum} && \text{Momentum}  \\
& x_i = x_{i-1} - \alpha \triangledown F(x_{i-1}) & & x_i = x_{i-1} - \alpha \left[ mv_{i-1}+\triangledown F(x_{i-1}) \right] \\
& \ \ \ = x_{i-1} - .7(2\cdot x_{i-1}) & & \ \ \ = x_{i-1}-.7\left[.2v_{i-1}+ 2 \cdot x_{i-1} \right] \\
& x_0 = 10   & & x_0  = 10  \\
& x_1 = 10-.7(2 \cdot 10) = -4 & & x_1 = 10 - .7\left[0.2 \cdot 0 + 2 \cdot 10 \right] = -4  \\
& x_2 = -4-.7(2 \cdot -4) = 1.6 & & x_2 = -4 - .7\left[0.2 \cdot 20 + 2 \cdot -4 \right]=-1.2  \\
& x_3 = 1.6-.7(2 \cdot 1.6) = -.64 & & x_3 = -1.2 - .7\left[0.2 \cdot -4 + 2 \cdot -1.2 \right]=1.04  \\
& \dots && \dots \\
\end{align*}
$$

Even with momentum added the algorithm is still oscillating due to the high learning rate, but with momentum added the absolute change between each epoch with different directions is dampened.

### Example C: Simple Linear Regression

As a third example we pull these concepts together and apply them to a simple linear regression using <abbr title="Random Reshuffling">RR</abbr>. The true values are $b_0 = 1$ and $b_1 = 1$ with initializations of $b_{0,1}=50$ and $b_{1,1}=50$. The maximum number of iterations was 10000 with a tolerance of $1e^{-4}$.

<img src="/images/posts/Momentum/LR.png" width="100%" style="padding:0px"/>

With the specified model parameters and hyperparemters the algorithm with no momentum failed to converge while the algorithm with $m=.6$ converged by epoch 5123. We can see that the momentum smooths out the algorithm with much fewer oscillations compared to the algorithm with no momentum. Additionally, we can see that the momentum is also helping increasing the step size compared to the no momentum algorithm.  

## Nesterov’s Accelerated Gradient (NAG)

The idea that led to momentum was that sharing information across epochs can allow gradient descent to speed up when back-to-back iterations go in the same direction or to dampen oscillations when rapidly changing direction. Momentum accomplishes this through remembering the values of the last epoch. Nesterov's Accelerated Gradient has the same goals as momentum, but accomplishes it through a different approach. Instead of the momentum term providing a 'correction' to the current gradient, NAG takes the current momentum and 'corrects' it by approximating the next epoch's gradient.

<img src="/images/posts/Momentum/NAG.png" width="100%" style="padding:0px"/>

Imagine we have calculated $\theta_{i-1}$ and are trying to calculate $\theta_i$ by using an approximate of the next gradient $\frac{\delta}{\delta \theta}F(\hat{\theta_{i}})$. The simplest method would be to simply use $\theta_i$ in the gradient rather than $\theta_{i-1}$;

$$
\begin{align*}
\theta_i & = \theta_{i-1} - v_i \\
v_i & = mv_{i-1} + \alpha \frac{\delta}{\delta \theta} F(\theta_{i}) \\
\end{align*}
$$

We could try and plug $\theta_i$ back into this function in order to estimate it, but then we end up back where we started with no solution for $\frac{\delta}{\delta \theta}F(\theta_i)$. In order to get out of this tautological nightmare we need to use some approximate measure $\hat{\theta}_i$ of $\theta_i$. An intuitive choice may be to calculate what the next $\theta$ would be while dropping the problematic unknown gradient. This results in the final form that is used below;

$$
\begin{align*}
\theta_i & = \theta_{i-1} - v_{i} \\
v_i & = mv_{i-1} + \alpha \frac{\partial}{\partial \theta} F(\hat{\theta}_i)\\
& = mv_{i-1} + \alpha \frac{\partial}{\partial \theta} F(\theta_{i-1}-mv_{i-1}) \\
\end{align*}
$$
