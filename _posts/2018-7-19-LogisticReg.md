---
layout: post
title: "Logistic Regression"
tags: MachineLearning
use_math: true
---

* TOC
{:toc}

## Introduction

Standard linear regression is a special case of a larger class of models called generalized linear models. These models allow for specifying different dependent variable distributions as well as link functions connecting the model errors. The most common generalized linear model is logistic regression, which has a binary dependent variable ($Y_i \in \\{ 0,1 \\} $). The reason for using a logistic regression instead of a standard linear regression in the case of a binary dependent variable is that a number of assumptions are otherwise violated; homogeneity of variance (as the variance of a binary variable changes based on the probability), normally distributed errors, and the predicted probability can extend beyond 0 and 1. At a large enough sample size the central limit theorem kicks in, addressing the homogeneity of variance  and error distribution assumptions, but it does not fix the range issue. These violations negatively influence the prediction (lower model accuracy) and inference (incorrect standard errors).

### Generalized Linear Models

Generalized linear models extend the general linear model framework to all exponential family distributions (normal, binomial, bernoulli, gamma, etc). They will not be discussed in detail in this post, and only a few details of their composition will be covered. These distributions can all be structured using the following form;

$$f_Y(y | \theta) = h(y)e^{\eta(\theta) \cdot T(y) - A(\theta)}$$

The observations and model parameters can be vectors, the important thing is that the function can be factorized when exponentiated. Each of these components has useful properties;

- $T(x)$ : A sufficient statistic of the distribution, or in otherwords a function of the data that can fully summarize the information about X.
- If $\eta(\theta) = \theta$ then we say that the distribution is in its canonical form. This is useful for identifying the canonical link functions for different distributions.   

There are two main changes when specifying a generalized linear model as compared to a general linear model; the specification of the dependent variable distribution and the link function between the coefficients and a distribution parameter. In the case of a standard linear regression, $F = N(\mu,\sigma^2)$ and the identity link function is used ($\mu_i = g(\eta_i) = \eta_i =  \beta_0 + \cdots + \beta_k x_{i,k}$). If we want to be general we can use the form below, wherein the data conditional on a linear combination of the covariates and coefficients follows some distribution $F$ with parameters $\theta$.

$$ y_i | x;\beta \sim F(\theta) $$

The link function $g(\eta_i)$ then connects the coefficients to some distribution parameter in F.
The choice of link function influences whether or not ordinary least squares can be used. If the link function is non-convex, such as with the sigmoid function/logit function, then there is no closed form solution to find the optimal $\beta$. The dependent variable distribution also change the form of the maximum likelihood estimate, as it is based on the probability of getting the observed data based on a set of distribution parameters $\theta$ given the distribution $F$.   

$$
\begin{align*}
& \theta_i = g(\eta_i) \\
& \eta_i = \beta_0 + \cdots + \beta_k x_{i,k}
\end{align*}
$$

One other key detail is the measure of goodness-of-fit called deviance. Deviance is a generalization of ordinary least squares for a normal dependent variable and is a function of the likelihood ratio test. The null model is fit with no additional variables, and the parameters are fit using the maximum likelihood estimates.

$$
\begin{align*}
\Lambda(y) & = \frac{L(\theta_0 | y)}{L(\theta_1|y)} : \text{Likelihood ratio test}\\
D(y,\hat{y}) & = 2ln(\Lambda(y)) = 2l(y|y)-2l(y|\hat{y}) : \text{Deviance} \\
\end{align*}
$$

Deviance can be interpreted as the degree of similarity of the fitted model with the simplest possible model. If the fitted model is the same as the null model then the $D(y,\hat{y})=2ln(1)=0$. The more the fitted model improves fit compared to the null model, the more negative the deviance, making the goal to minimize the deviance for a chosen model. As an example of how it is used, here we calculate the deviance for a normal dependent variable and end up with the sum of squares.

$$
\begin{align*}
f(y|\mu,\sigma^2) & = \frac{1}{\sqrt{2\pi\sigma^2}}e^{-\frac{(y-\mu)^2}{2\sigma^2}} \\
& \implies l(y|\hat{y}) = -\frac{1}{2}ln(2\pi\sigma^2)-\frac{(y-\mu)^2}{2\sigma^2} \\
& \implies l(y|y) = -\frac{1}{2}ln(2\pi\sigma^2) \\
D(y,\hat{y}) & = \frac{(y-\mu)^2}{\sigma^2} \propto (y-\mu)^2 \\
\end{align*}
$$

## Logistic Regression

While it may not be immediately obvious, we can easily show that the binomial distribution (an extension of the bernoulli distribution but with n trials) belongs to the exponential family of distributions.

$$
\begin{align*}
f_X(x | \theta) & = h(x)e^{\eta(\theta) \cdot T(x) - A(\theta)} : \text{Exponential Family Form} \\
f(x|p) & = \left( \begin{matrix} n \\ x \end{matrix} \right) p^x(1-p)^{n-x} : \text{Binomial Distribution} \\
& \implies e^{f(x)} = \left( \begin{matrix} n \\ x \end{matrix} \right) e^{x\text{log}\left(\frac{p}{1-p}\right)+nlog(1-p)} : \text{Exponential Form} \\
& \implies h(x) = \left( \begin{matrix} n \\ x \end{matrix} \right) : \text{Normalizing Constant} \\
& \implies T(x) = x \\
& \implies \eta(\theta) = \text{log}\left(\frac{p}{1-p}\right) : \text{Canonical Link Function} \\
& \implies A(\theta) = nlog(1-p) \\
\end{align*}
$$

### Dependent Variable Distribution

When choosing a distribution for a dependent variable we want to ensure that they both cover the same range, in this case $Y_i \in \\{ 0,1 \\} $. One reasonable choice would then be the binomial distribution with 1 trial, which is the Bernoulli distribution. This starts us off with a fair amount of information;

$$ \begin{align*}
& y_i | x; \beta \sim Bern(p) \\
& \implies E(y_i) = p_i = g(\eta_i) \\
& \implies P(y=1|x;\beta)=g(\eta_i) \\
& \implies P(y=0|x;\beta)=1-g(\eta_i) \\
& \implies P(y_i|x;\beta) = g(\eta)^{y_i}(1-g(\eta_i))^{1-y_i} \\
\end{align*}
$$

### Link Function

There are a few desirable characteristics we want in our link function. We want it to be monotone increasing and differentiable so that it can be easily optimized, and invertible so that we can easily get both the estimated coefficients and predicted value. With the dependent variable distribution chosen, we now want to select a link function;

$$p_i = g(\eta_i)$$

We know that we want to allow $\eta_i$ to take on any real value and restrict $p_i$ to legal probability values.

$$\{p_i : \eta_i \in [-\infty,\infty]\} \in [0,1]$$

A good choice for this would be the logit function, a specific sigmoid function that constrains its input across any range between 0 and 1.

$$ \begin{align*}
& p_i = g(\eta_i)=logit(\eta_i)=\frac{e^{\eta_i}}{1+e^{\eta_i}}= \frac{1}{1+e^{-\eta_i}}=\sigma(\eta_i) \\
& \Longleftrightarrow p_i(1+e^{\eta_i}) = e^{\eta_i} \\
& \Longleftrightarrow p_i = e^{\eta_i}-pe^{\eta_i} \\
& \Longleftrightarrow \frac{p_i}{1-p_i} = e^{\eta_i} \\
& \Longleftrightarrow ln(\frac{p_i}{1-p_i})=ln(Odds)=\eta_i \\
\end{align*}
$$

There are a number of other link functions which meet these requirements, such as probit (inverse of the normal cumulative distribution function) and complementary log-log function, but those aren't discussed here.

### Estimation

#### Maximum Likelihood

Generalized linear models use maximum likelihood to estimate the coefficients in the model. For logistic regression this is pretty straightforward;

$$
\begin{align*}
L(\beta) & = \prod^N_{i=1} g(\eta_i)^{y_i}{(1-g(\eta_i))}^{1-y_i} \\
l(\beta) & = ln(L(\beta)) = \sum^N_{i=1}y_i ln(g(\eta_i))+\sum^N_{i=1}(1-y_i)ln(1-g(\eta_i))\\
\end{align*}
$$

This value can be averaged, just as in ordinary least squares, resulting in;

$$\frac{1}{N}l(\beta)= \frac{1}{N}\sum^N_{i=1}y_i ln(g(\eta_i))+\frac{1}{N}\sum^N_{i=1}(1-y_i)ln(1-g(\eta_i))$$

We then find the solution numerically, as there is no closed form expression unlike in ordinary least squares. This can be done using a number of methods, for example Newton-Raphson, gradient descent, iteratively weighted least squares, or Fisher scoring. It is important to note that when the canonical link function is used all of these methods are equivalent (see McCullagh and Nelder 1989 for details), and iteratively least squares is often preferred for ease of implementation and speed.

#### Deviance

The maximum likelihood estimate for each observation is $\hat{p}_0 = \frac{y_i}{n}=y_i$ leading to the following formula for deviance.

$$
\begin{align*}
D(y,\hat{y}) = 2\sum^N_{i=1}\left[y_iln\left(\frac{y_i}{\hat{y_i}}\right)+(1-y_i)ln\left(\frac{1-y_i}{1-\hat{y_i}}\right) \right]
\end{align*}
$$

## Convergence

The use of numerical optimization rather than analytical optimization introduces a need to check that the model estimates have converged. Estimates that did not converge should not be trusted for inference, and may be sub-optimal for prediction. Logistic regression's log-likelihood is globally concave, in other words lacking local extremum, making convergence easier by not needing to worry about the optimization getting stuck in local extreme values. Lack of convergence can be due to a number of reasons, a few of which are covered here.

### Large Ratio of Predictors to Sample Size

The larger the ratio of predictors to sample size the harder it is to optimize due to the curse of dimensionality. If the number of predictors is larger than the overall sample size some form of regularization or bayesian methods are required to estimate the model at all.

### Non-Standardized Covariates

Covariates on largely different scales should be standardized before including in the model. Standardization makes the estimation surface more spherical and easier to navigate, while variables on wildly different scales can cause large 'troughs' that are hard to optimize.

### Multicollinearity

The more correlated the covariates are, the more possible solutions that exist for combinations of the correlated variables. This makes it hard to find the 'right' answer as numerous reasonable alternatives will exist, and makes estimation more challenging.

### Sparseness

An excess of 0s amongst the covariates, particularly categorical variables, can cause estimation issues as the natural log found in the maximum likelihood cost function is undefined at 0. This may occur when there are numerous categorical variables for each many combinations are empty, or there are a large number of zeroes in a continuous variable.  

$$
\text{Dense :}
\left[
\begin{matrix}
3.2 & 0 & 10 & 17 & 0.01 \\
9.1 & 0 & 20 & 12 & -2.34 \\
6.0 & 1 & 20 & 9 & 1.67 \\
4.1 & 1 & 50 & 7 & 0.09 \\
2.0 & 0 & -40 & 20 & 3.04 \\
\end{matrix}
\right]
$$

$$
\text{Sparse :}
\left[
\begin{matrix}
0 & 0 & 0 & 17 & 0 \\
0 & 0 & 0 & 0 & -2.34 \\
0 & 0 & 0 & 0 & 0 \\
0 & 1 & 50 & 0 & 0 \\
2.0 & 0 & 0 & 20 & 0 \\
\end{matrix}
\right]
$$


### Complete Separation

A case of too much of a good thing occurs when a linear combination of covariates can perfectly predict the dependent variable. This is known as complete separation, and when it is present there is no maximum likelihood estimate and the optimization will not converge. When this occurs one should investigate their data closely as likely some mistake has been made or feature leakage may be present.

## Interpretation

The coefficients in a logistic regression are log odds-ratios, and when exponentiated can be interpreted as the value that the odds are multiplied by for every one unit increase in that covariate.

$$
\begin{align*}
& \text{Log Odds}: ln \left( \frac{p_i}{1-p_i} \right) = \beta_0 + \beta_1x_{i,1} + \cdots + \beta_kx_{i,k}\\
& \text{Odds} : \frac{p_i}{1-p_i} = e^{\beta_0 + \beta_1x_{i,1} + \cdots + \beta_kx_{i,k}} \\
& \text{Probability} : p_i = \frac{1}{1+e^{-(\beta_0 + \beta_1x_{i,1} + \cdots + \beta_kx_{i,k})}} \\
& X_k \text{ Odds Ratio} : \frac{\text{odds}(x_k+1)}{\text{odds}(x_k)} = \frac{\beta_0 + \beta_1x_{i,1} + \cdots + \beta_k(x_{i,k}+1)}{\beta_0 + \beta_1x_{i,1} + \cdots + \beta_kx_{i,k}} = e^{\beta_k}
\end{align*}
$$

Interpreting model fit in logistic regression requires some additional tools compared to standard linear regression. The standard residual calculation $y_i - \hat{y}$ is not appropriate since the variance of the estimated value changes depending on the probability. There are a number of alternative forms of residuals that can be used, two of which are briefly described here; pearson residuals and deviance residuals.

<img src="/images/posts/LogisticRegression/residual_types.png" width="100%" style="padding:0px"/>

### Pearson Residuals

A simple solution to the problem of heteroskedasticity for residuals is to standardize them, forming Pearson residuals.

$$
r_i = \frac{y_i - \hat{p}_i}{\sqrt{\hat{p}_i(1-\hat{p}_i)}}
$$

### Deviance Residuals

Another option for calculating residuals is to decompose the overall deviance into a sum of the deviance for each observation. In order for this to be equivalent to traditional residuals, the observation deviances square rooted and multiplied times the sign of the difference of ($y - \hat{y}$).

$$
\begin{align*}
d_i & = \text{sign}_i\sqrt{-2\big(y_iln\hat{y}_i+(1-y_i)ln(1-\hat{y}_i)\big)} \\
\text{sign}_i & =
\begin{cases}
1, & \text{if } y_i = 1 \\
-1, & \text{otherwise} \\
\end{cases} \\
\end{align*}
$$

### Goodness-of-Fit Metrics

There is no exact corollary to $R^2$ in linear regression for logistic regression. This is because the data in logistic regression is by definition heteroskedastic with different error variances dependending on the probability/predicted value. This means that each residual is not counted equally, as some are more influential than others, so the $R^2$ cannot be considered the universal measure of goodness-of-fit. For this reason all coefficients of determination in logistic regression are $\text{pseudo-}R^2$.

There are a few desirable properties we would like in our $\text{pseudo-}R^2$; $\text{pseudo-}R^2 \in (0,1)$, monotonically increasing with model fit/odds ratio, independent of base rate, and ease of interpretation. A few $\text{pseudo-}R^2$ have been proposed, each with their own weaknesses and strengths. A few forms are described here.

#### Likelihood Ratio $R^2_L$

The simplest and most intuitive form takes the ratio of deviances scores between the null model (only intercept) and the fitted model. This can be interpreted similarly to the traditional $R^2$, however, it is not always monotonically increasing.

$$R^2_L = \frac{D_{Null}-D_{Fitted}}{D_{Null}}$$

#### Cox and Snell $R^2_{CS}$

Another idea is to look at the ratio of likelihoods between the fitted model and the null model.

$$R^2_{CS} = 1 - \left(\frac{L_{Fitted}}{L_{Null}}\right)^{2/n}$$

#### Tjur's $R^2_D$

Tjur (2009) took a very different approach, and instead thought about how an ideal model would have much of the predicted mass for 'successes' with high values, and the predicted mass for 'failures' with low values. Consequently and non-obviously, this formulation is asymptotically equivalent to traditional $R^2$ values.  

$$R^2_D = \bar{\hat{y}}_{y_i=1}-\bar{\hat{y}}_{y_i=0}$$

## Assumptions

The assumptions for logistic regression are largely the same as in standard linear regression with a few differences. There is no assumption of homogeneity of variance, since with a bernoulli distribution the variability changes as a function of the probability by definition. The errors are also assumed to follow a bernoulli distribution rather than a normal distribution.
