---
layout: post
title: "Bayesian State Space Model of Simulated Satellite Data"
categories: Time Series
---

## Overview



## Data

<img src="/images/ApplBayesSS/rawplot.png" width="100%" style="padding:2px"/>

<img src="/images/ApplBayesSS/missplot.png" width="100%" style="padding:2px"/>

<img src="/images/ApplBayesSS/corplot.png" width="100%" style="padding:2px"/>

## Statistical Model

One way to view the data is to imagine that there is an unobserved latent process &theta; with process error, and that the measurements we receive from each of the three satellites are observed realizations from this process. The &theta; errors were considered independent across time, and the satellites errors were considered to be independent and to follow a Gaussian distribution. Using this information we can recognize this as a Linear Gaussian State Space model, or a Dynamic Linear Model. There is a large portion of missing data in each of the satellites, however this is easily handled when the problem is cast from a bayesian state space perspective, as we can treat the satellites as dependent variables, which JAGS handles well. JAGS can only handle missing data in univariate variables however, so Y1 was cast as 6 variables, one for each pixel, though the full covariance matrix was still fitted and estimated to account for their relationship.

<div style="text-align:center;"><img src="/images/ApplBayesSS/equations.png" width = "50%" style="padding:2px" align="middle"/></div>

Two models were initially compared to see if correlation needed to be different across each of the &theta;, or if we could use one &rho; for all pixels. The single &rho; model fit essentially the same for mean deviance (mean deviance = 1072, penalty = 1020, penalized deviance 2092) as the 6 &rho; model (mean deviance = 1037, penalty = 12915, penalized deviance = 13952), and the six &rho; had essentially an identical posterior distribution, so the simpler model was selected. Then a model with an additive bias (intercept) was compared to a model with multiplicate and additive bias (intercept + slope); the intercept + slope model (mean deviance = -617, penalty = 1105, penalized deviance = 487.7) fit far better then the just intercept model (mean deviance = 1072, penalty = 1020, penalized deviance = 2092), and was selected for the final model to be used.

## JAGS code

Repetitive sections were edited in order to fit the page constraints.

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

## Covergence Diagnostics

The maximum value of the gelman statistic, measuring convergence across chains, was 1.048 across all Theta for each time point and pixel. The samplesizes in general looked acceptable, however a few can be seen to be below 1000. The minimum was around 800, and given the nature of the assignment and the computational strain of thinning, which crashed my computer twice, this was considered sufficient for the purpose of the midterm. Trace plots were also visually inspected and had all converged across all three chains after the burnin had been removed (5000 samples across each chain).  

<img src="/images/ApplBayesSS/sampplot.png" width="100%" style="padding:2px"/>


## Final Results

The black line below represents the estiamted values, the red squares are observed for satellite 1, the blue circles are observed for satellite 2 (biased), and the brown plus' are observed for satellite 3. The fit appears to be very good across all values except blue, which we would expect as it was biased (displaying a intercept and slope difference from the predicted).

<img src="/images/ApplBayesSS/posterior.png" width="100%" style="padding:2px"/>
