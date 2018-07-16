---
layout: post
title: "Standard Linear Regression"
tags: MachineLearning
use_math: true
---

* TOC
{:toc}

## Introduction

Linear regression was one of the first models developed in statistics. It is backed by a strong theoretical foundation and due to the general success of linear approximations it is still well used today. When the assumptions are met, it can be shown that linear regression is the best linear unbiased estimator (BLUE), a very desirable property. Linear regression forms the backbone of many more modern methods, and is a required skill for any analyst. There is an enormous breadth of material associated with linear regressions, and as such this post aims to primarily be a primer on their construction and use.  

The intuition behind it is fairly straightforward; given some variable $y$, we want to model it as accurately as possible through a linear combination of some covariates $X$ using coefficients $\beta$.

$$
\begin{align*}
y_i & = \beta_0+\beta_1 x_{i,1} + \cdots + \epsilon_i \\
y & = X\hat{B} + \epsilon \\
\end{align*}
$$

The random noise associated with $y$ is delegated to $\epsilon_i$; the better the fit, the smaller the $\epsilon_i$.

<img src="/images/LinearRegression/lm_example.png" width="100%" style="padding:0px"/>

## Estimating Coefficients

The two primary approaches to estimating linear regression and many other methods are ordinary least squares and maximum likelihood. While the results are identical in the case of normality meeting the necessary conditions, ordinary least squares relies upon no parametric assumptions, but does not provide distributional information necessary for inference. Maximum likelihood on the other hand uses assumptions about the distribution of the data to construct confidence intervals and hypothesis tests. A brief overview of the two methods is provided here.

### Ordinary Least Squares

Ordinary least squares can be shown under certain conditions (identical and independently distributed errors, strict exogeneity, correct specification) to be the best possible unbiased linear estimator. Additionally, when the errors are normally distributed, it is identical to the maximum likelihood estimator.

$$
\begin{align*}
SSE & = \sum_{i=1}^N (y_i -\hat{y})^2 = \sum_{i=1}^2 \epsilon_i^2 = \epsilon^T\epsilon \\
& = (Y-X\hat{B})^T(Y-X\hat{B})=Y^TY -2Y^TX\hat{B} +\hat{B}^TX^TX\hat{B} \\
0 & = \frac{\partial}{\partial \hat{B}}(Y^TY -2Y^TX\hat{B} +B^TX^TX\hat{B}) \\
& = -2X^TY+2X^TX\hat{B} \\
& \implies -2X^TX\hat{B} = -2X^TY \\
& \implies X^TX\hat{B} = X^TY \\
& \implies \hat{B}=(X^TX)^{-1}X^TY
\end{align*}
$$

We can easily show that $\hat{B}$ is an unbiased estimator of $B$ assuming that $X\bot\epsilon$;

$$
\begin{align*}
\hat{B} & = E[(X^TX)^{-1}(X^TY)] \\
& = E[(X^TX)^{-1}X^T(XB+\epsilon)] = B+E[(X^TX)^{-1}X^T\epsilon] \\
& = B \\
\end{align*}
$$

The time complexity of ordinary least squares is $O(n^3)$ due to the $(X^TX)^{-1}$, though there are numerical methods to more quickly find approximate solutions to the inverted matrix.

### Maximum Likelihood

If the conditions for ordinary least squares are met and we additionally assume that the residuals are normally distributed or that the sample size is large enough for them to be asymptotically normal, we get the same solution for estimating the optimal coefficients, but with the addition of distributional information about the model parameters.

$$ \begin{align*}
& y_i | x,\beta \sim N(\mu,\sigma_\epsilon^2) \\
& \mu_i = \beta_0 + \cdots + \beta_k x_{i,k}  \in [-\infty,\infty]\\
& P(y_i) = \frac{1}{\sqrt{2\pi \sigma_\epsilon^2}}e^{-\frac{(y_i - \mu_i)^2}{2\sigma_\epsilon^2}} \\
\end{align*}
$$

An intuitive way to estimate the coefficients would be to choose the values that would be most likely given the observed data. Maximizing the log likelihood function reveals the familiar sum of squares and mean squared error. In this case, since the function is being maximized, the function is negative.

$$ \begin{align*}
L(\beta) & =\prod^N_{i=1} \frac{1}{\sqrt{2\pi \sigma^2}}e^{-\frac{(y_i - \mu_i)^2}{2\sigma^2}} \\
& = \frac{1}{(2\pi \sigma)^{N/2}} e^{-\frac{\sum^N_{i=1}(y_i - \mu_i)^2}{2\sigma^2}} \\
ln(L(\beta))= l(\beta) & = (-N/2)ln(2\pi \sigma) -\frac{\sum^N_{i=1}(y_i - \mu_i)^2}{2\sigma^2}\\
& \propto -\sum^N_{i=1}(y_i - \mu_i)^2 \\
& \propto -\frac{1}{N} \sum^N_{i=1}(y_i - \mu_i)^2 \\
\end{align*}
$$

Minimizing this function without the negative provides the same result, and the more familiar form.

$$
\max_{\beta \in R^k}(-\frac{1}{N} \sum^N_{i=1}(y_i - \mu_i)^2) = \min_{\beta \in R^k}(\frac{1}{N} \sum^N_{i=1}(y_i - \mu_i)^2))
$$

Estimation for general linear regression is particularly easy as there is a closed form for finding the , subverting the need to numerically optimize this equation.

## Interpretation

One of the primary draws of linear regression is the ease of inference/interpretability; each slope can be interpreted as the $\beta_k$ amount of change in $Y_i$ with a 1 unit increase in $X_{k,i}$ controlling for all other covariates.

Categorical variables with J groups are estimated through creating $J-1$ dummy coded variables (0 or 1). The reason we don't use $J$ dummy coded variables is that one term could be created from a linear combination of the others, preventing the inversion of $X^TX$. The group that does not receive a dummy coded variable is instead set as the 'reference' variable and is essentially incorporated into the slope. Each categorical variable can then be interpreted as the $\beta_k$ amount of difference from the reference group in $Y$.

### $R^2$

A convenient metric for assessing the overall quality of fit of the model to the data is the coefficient of determination, or $R^2$, which is the proportion of the variance in $Y$ explained by $X$. This can be calculated through decomposition the data variability into three components; the total sum of squares $SS_{tot}$, regression sum of squares $SS_{reg}$, and the residual sum of squares $SS_{res}$.

$$
\begin{align*}
SS_{tot} & = \sum_{i=1}^N(y_i - \bar{y})^2 = Var(y) = SS_{reg} + SS_{res} \\
SS_{reg} & = \sum_{i=1}^N(\hat{y} - \bar{y})^2 \\
SS_{res} & = \sum_{i=1}^N(\hat{y} - y_i)^2 \\
\end{align*}
$$

The choice of these deviations makes more sense when visualized. Below are the deviations of each time, which are then squared and summed to form our model variability components.

<img src="/images/LinearRegression/ss_examples.png" width="100%" style="padding:0px"/>

With this in mind, calculating and interpreting $R^2$ is very straightforward. It is bound the proportion of the overall variance of the data that is explained using the model bounded between 0 (model explains none of the data) and 1 (model perfectly explains the data).

$$ R^2 = 1 - \frac{SS_{res}}{SS_{tot}} = \frac{SS_{reg}}{SS_{tot}}$$

### Standard Error

Simple interpretation of the coefficients is one of the strong points of linear regression; however, coefficients should be interpreted with some measure of probability of a value, either using p-values of confidence intervals. Standard errors (a fancy name for variance of a parameter) are relatively straightforward to derive and conveniently can be calculated using values that have been used in other components ($SS_{res}$ and $(X^TX)^{-1}$).

$$
\begin{align*}
Var[\hat{\beta}] & = SE[\hat{\beta}] = E[(\hat{\beta}-\beta)(\hat{\beta}-\beta)^T] \\
& = E\big[\big((X^TX)^{-1}(X^TY)-(X^TX)^{-1}(X^T(X\beta+\epsilon)\big)\big((X^TX)^{-1}(X^TY)-(X^TX)^{-1}(X^T(X\beta+\epsilon)\big)^T\big] \\
& = E[((X^TX)^{-1}X^T\epsilon)((X^TX)^{-1}X^T\epsilon)^T] \\
& = E[(X^TX)^{-1}X^T\epsilon\epsilon^TX(X^TX)^{-1}] \\
& = (X^TX)^{-1}X^TE[\epsilon\epsilon^T]X(X^TX)^{-1} \\
& = (X^TX)^{-1}X^T\sigma^2_{\epsilon} IX(X^TX)^{-1} \\
& = \sigma^2_{\epsilon}I(X^TX)^{-1} \\
\hat{\sigma}^2_{\epsilon} & = \frac{SS_{res}}{N-K} = MSE \\
\widehat{Var(\hat{\beta})} & = \hat{\sigma}^2_{\epsilon}I(X^TX)^{-1}
\end{align*}
$$

## Assumptions

There are a number of assumptions associated with the ordinary least squares estimates and the maximum likelihood estimates which are briefly covered here. Methods exist to address violations of assumptions, however, these are outside of the scope of this post. The intention here is to give an idea of why these assumptions exist and how they influence the model results.

### Independent and Identically Distributed (iid) errors

This expression is a mouthful, however, it can be broken down into the following three components;

$$
\begin{align*}
& \epsilon_i \sim iid(0,\sigma^2_\epsilon) \\
& \implies E[\epsilon_i] = 0 \\
& \implies Var(\epsilon_i)=\sigma^2 : \forall i \\
& \implies \epsilon_i \bot \epsilon_j : i \ne j \\
\end{align*}
$$

#### Unbiased: $E[\epsilon_i] = 0$

The residuals are assumed to be centered around the true value across all data points. In other words, that our estimates are unbiased. Model misspecification is one of the primary causes of violating this assumption, such as omitting a key variable in the model or a meaningful interaction. Violating this assumption can have a negative influence on the accuracy of the estimates, the standard errors, and prediction.

<img src="/images/LinearRegression/bias_unbiased.png" width="100%" style="padding:0px"/>
<img src="/images/LinearRegression/bias_bias.png" width="100%" style="padding:0px"/>

This assumption is the same as stating that Y must be a linear function of the parameters, but not necessarily linear in and of itself. If it is linear in its parameters, then $E[\epsilon_i] = 0$. The $E[\epsilon]=0$, as the overall mean of residuals remains 0, however, the expectation across the different residuals varies and is not always equal to 0 in the biased case.

#### Homogeneity of variance: $Var(\epsilon_i)=\sigma^2 : \forall i$

Non-constant variance, or heteroskedasticity, is important as it results in observations with higher variance being weighed more than observations with lower variance. There are many forms of heteroskedasticity, such as the one below;

<img src="/images/LinearRegression/heteroskedasticity_example.png" width="100%" style="padding:0px"/>

<img src="/images/LinearRegression/heteroskedasticity_cost.png" width="100%" style="padding:0px"/>


There are two primary consequences of heteroskedasticity in relation to model fit; the ordinary least squares estimator is no longer efficient (the standard error may no longer approach zero as sample size increases) and the standard errors may be biased.

#### Independent errors: $\epsilon_i \bot \epsilon_j : i \ne j$

Two of the most common cases in which we would expect errors to not be independent are spatial and time series data. Violating this assumption does not bias the estimated coefficients, but does bias the standard errors.

$$ SE[\hat{\beta}] = (X^TX)^{-1}X^TE[\epsilon\epsilon^T]X(X^TX)^{-1} \ne  (X^TX)^{-1}X^T\sigma^2_{\epsilon} IX(X^TX)^{-1} $$

Models that explicitly model autocorrelation, such as box-jenkins models for time series data, also provide more accurate predictions by incorporating this information compared to ignoring the autocorrelation using a standard linear regression.  

$$
\begin{align*}

\end{align*}
$$

### Outliers

Extreme values can be considered either as reasonable but low probability values from the same distribution as the rest of the observations, or as extreme values drawn from a separate distribution. Unfortunately there is no explicit way to know whether or not an outlier is an extreme but reasonable value or a non-representative value; for this reason it is not advised to simply remove all extreme observations. Unfortunately, they can have a large influence on the ordinary least squares estimates. The least squares method increases the importance of extreme values by squaring the errors, allowing them to have excessive leverage on the estimates.

<img src="/images/LinearRegression/outlier_example.png" width="100%" style="padding:0px"/>

### Normally distributed errors

Standard linear regression assumes that the residuals of the linear model follow a normal distribution. This can be easily checked through a QQ plot, comparing the model residuals to expected residuals from a normal distribution. As long as there is a moderate sample size linear regression is quite robust to violations of this assumption. OLS does not require this assumption to be the best linear unbiased estimator (BLUE), but it is required for hypothesis testing in small sample sizes. The estimated coefficients will remain unbiased and the prediction accuracy will be about the same, but the confidence and prediction intervals may become less accurate. Below we see an example of a QQ plot with normal residuals and gamma distributed residuals, both with the same variance. The gamma residuals depart heavily from the normal residuals in the higher quantiles here.

<img src="/images/LinearRegression/normerror_qq.png" width="100%" style="padding:0px"/>

### Weak exogeneity

One of the less obvious assumptions in linear regression is that the covariates are measured without error, or weak exogeneity (the name comes from another term for covariates; exogenous variables). Violating this assumption can bias estimated coefficients and standard error.

$$
\begin{align*}
Y & = \beta X + \epsilon_y : \text{True data generating model with X} \\
Z & = X + \epsilon_z  : \text{Z as a noisy measurement of X}\\
& \implies E[X^T\epsilon_y] = 0 \\
& \implies E[\epsilon_z^T\epsilon_y] = 0 \\
\hat{Y} & = \beta Z + \epsilon_y : \text{Model using noisy Z}\\
E[\hat{\beta}] & = E[(Z^TZ)^{-1}(Z^{T}Z\beta)] \\
&  = E[\big((X+\epsilon_z)^T(X+\epsilon_z)\big)^{-1}(X+\epsilon_z)^{T}(X+\epsilon_z)\beta] \\
& = E[(X^TX +2X^T\epsilon_z+\epsilon_z^T\epsilon_z)^{-1}(X^TX +2X^T\epsilon_z+\epsilon_z^T\epsilon_z)\beta] \\
& \ne \beta : \text{Since } E[\frac{1}{X}] \ne \frac{1}{E[X]} \text{(Jensen's Inequality)} \\
\end{align*}
$$  

This is the most commonly violated assumption and is rarely met in most situations where the covariates can't be perfectly controlled or measured.

<img src="/images/LinearRegression/exogeniety.png" width="100%" style="padding:0px"/>

### Low multicollinearity

A high degree of correlation between covariates, known as multicollinearity, can be an issue when estimating the coefficients as there are many possible solutions. This makes calculating $(X^TX)^{-1}$ challenging and the resulting coefficients and standard errors can become very unstable. This can be addressed through adding in additional constraints using regularization methods (ridge regression, lasso, etc) or bayesian methods.

## Scaling variables

A final note is that while scaling variables isn't required for linear regression, it is often a good practice as it can help ease estimation of the coefficients. This becomes more of a problem when a closed form solution for estimating coefficients doesn't exist, such as in logistic regression.
