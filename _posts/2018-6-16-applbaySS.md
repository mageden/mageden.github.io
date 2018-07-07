---
layout: post
title: "Bayesian State Space Model of Simulated Satellite Data"
tags: TimeSeries Bayesian
---

* TOC
{:toc}

## Overview

An interesting problem that comes up commonly in ecology and soil sciences is how to estimate an unobserved latent process over time through data coming in through multiple sources. This problem is made more challenging with commonly high proportions of missing data. The standard Box-Jenkins approach is not able to estimate the unobserved process/state, requiring the use of an alternative method; state space models. State space models, or dynamic linear models, are a flexible group of models in which we define a measurement model of the state of the system and a transition model for how that state changes over time. An upcoming post will dive into how these work and some of the things that are able to do. Bayesian implementations of these allow for us to investigate the posteriors for the state space parameters and unobserved variables, as well as easily handle missing data.

This post gives a brief example on the use of bayesian state space models in R using JAGS in order to highlight how powerful these models can be in certain situations.

## Data

The data for this study were simulated and provided by a professor in my bayesian course. The data were simulating satellite measurements of the Normalized Difference Vegetation Index (NDVI), a measure of "greenness" of a region, across 365 days. The true value of NDVI was generated across 6 pixels using the following structure;  

<div style="text-align:center;"><img src="/images/ApplBayesSS/NDVIformula.png" style="padding:2px" align="middle"/></div>

The fake data came from 3 satellites. The mechanisms for the missing data were not given (MCAR, MAR, NMAR), and the proportion varied across satellite.

1. Satellite 1 was an unbiased but noisy measurement across all pixels with approximately 80% of the data missing.
2. Satellite 2 was a potentially biased and noisy measurement for only pixel 1 with approximately  10% missing data.  
3. Satellite 3 was a potentially biased and noisy measurement with approximately 10% missing data. This satellite measured the entire region as an arithmetic average.  

Below is a plot of the data over time across pixel and satellite. No clear patterns were present for the missing data across any of the satellites, so the mechanism was assumed to be MCAR.

<img src="/images/ApplBayesSS/rawplot.png" width="100%" style="padding:2px"/>

Proportion of missing data by satellite and pixel. Closely but not perfectly matches the proportions provided, suggesting that the missing data process was at least random.

<img src="/images/ApplBayesSS/missplot.png" width="100%" style="padding:2px"/>

Scatterplot matrix of the various measurements. Distributions appear approximately normal with high correlation across pixels/satellites.

<img src="/images/ApplBayesSS/corplot.png" width="100%" style="padding:2px"/>

## Statistical Model

The &theta; errors were independent across time, and the satellites errors were considered to be independent and to follow a Gaussian distribution. Using this information we can recognize this as a Linear Gaussian State Space model, or a Dynamic Linear Model. JAGS was used to estimate the model, as it is able to handle missing data in the dependent variable. JAGS can only handle missing data in univariate variables however, so Y1 was cast as 6 variables, one for each pixel, though the full covariance matrix was still fitted and estimated to account for their relationship.

<div style="text-align:center;"><img src="/images/ApplBayesSS/equations.png" width = "50%" style="padding:2px" align="middle"/></div>

Two models were initially compared to see if correlation needed to be different across each of the &theta;, or if we could use one &rho; for all pixels. The single &rho; model fit essentially the same for mean deviance (mean deviance = 1072, penalty = 1020, penalized deviance 2092) as the 6 &rho; model (mean deviance = 1037, penalty = 12915, penalized deviance = 13952), and the six &rho; had essentially an identical posterior distribution, so the simpler model was selected. Then a model with an additive bias (intercept) was compared to a model with multiplicative and additive bias (intercept + slope); the intercept + slope model (mean deviance = -617, penalty = 1105, penalized deviance = 487.7) fit far better then the just intercept model (mean deviance = 1072, penalty = 1020, penalized deviance = 2092), and was selected for the final model to be used.

## JAGS code

Repetitive sections were replaced with ellipses for brevity.

```r
model_string <- "model{
# ---- Likelihood ----
# Initialize Theta
Theta[1, 1:6] ~ dmnorm(muTheta[1, 1:6], Omega1[1:6, 1:6])
muTheta[1, 1:6] <- muTheta1[1:6]
# Create Theta Chain
for(i in 2:n) {
    Theta[i, 1:6] ~ dmnorm(muTheta[i, 1:6], Omega2[1:6, 1:6])
    muTheta[i, 1:6] <- muTheta2[1:6] + Theta[i-1,1:6] * rho}
# Satellites
for(j in 1:n){
    for(c in 1:6) {
        Y1[j,c] ~ dnorm(Theta[j,c], lambdaY1[c])
    }
    Y2[j] ~ dnorm(Theta[j, 1] * mult.errY2 + muY2, inv.var.y2)
    Y3[j] ~ dnorm(mean(Theta[j,1:6]) * mult.errY3 + muY3, inv.var.y3)}
# ---- Priors ----
Omega1[1:6, 1:6] ~ dwish(W,6.1)
Omega2[1:6, 1:6] ~ dwish(W,6.1)
for(k in 1:6) {
    muTheta1[k] ~ dnorm(0, .0001)   # Mu terms for Theta1
    muTheta2[k] ~ dnorm(0, .0001)   # Mu terms for Theta2
    lambdaY1[k] ~ dgamma(.01, .01)   # Starting inv. var for Y1
    sigmaY1[k] <- 1/sqrt(lambdaY1[k]) # Invert for easy calculations}
# Temporal AutoCorrelation
rho ~ dunif(-1,1)
# Univariate Mu
muY2 ~ dnorm(0, .0001)
muY3 ~ dnorm(0, .0001)
mult.errY2 ~ dnorm(0, .0001)
mult.errY3 ~ dnorm(0, .0001)
# Inverse Variance
inv.var.y2 ~ dgamma(.01, .01)
inv.var.y3 ~ dgamma(.01, .01)
# Priors for Y1, done separately since can't have missing data in multivariate node
rho12 ~ dunif(-1,1)
rho13 ~ dunif(-1,1)
...
# Create Y1 cov. matrix with constraints....don't invert due to positive definite issues
InvOmegaY1[1,1] <- 1/lambdaY1[1]
InvOmegaY1[1,2] <- rho12 * sigmaY1[1] * sigmaY1[2]
...
"
```

## Convergence Diagnostics

The maximum value of the gelman statistic, measuring convergence across chains, was 1.048 across all Theta for each time point and pixel. The sample sizes in general looked acceptable, however a few can be seen to be below 1000. The minimum was around 800, and given the nature of the assignment and the computational strain of thinning, which crashed my computer twice, this was considered sufficient for the purpose of the midterm. Trace plots were also visually inspected and had all converged across all three chains after the burn-in had been removed (5000 samples across each chain).  

<img src="/images/ApplBayesSS/sampplot.png" width="100%" style="padding:2px"/>


## Final Results

The black line below represents the estimated values, the red squares are observed for satellite 1, the blue circles are observed for satellite 2 (biased), and the brown plus' are observed for satellite 3. The fit appears to be very good across all values except blue, which we would expect as it was biased (displaying a intercept and slope difference from the predicted).

<img src="/images/ApplBayesSS/posterior.png" width="100%" style="padding:2px"/>
