---
layout: post
title: "Introduction to Big-O Notation for Time Complexity"
tags: Programming
use_math: true
---

* TOC
{:toc}

## Introduction

As the scale and complexity of an analytical problem grows it is important to understand the computational burden that comes with training, tuning, and predicting models. One of the more common notations used is Big-O notation, which summarizes the efficiency of an algorithm relative to its input as the input becomes arbitrarily large (execution time, space used, etc). The O stands for order, as we are interested in only the fastest growing component of the growth rate of the function. It can be used to compare the efficiency of different approaches and evaluate the trade-off between computational burden and model accuracy (it may not be worth using a method that takes $T^2$ as long as another with only a .0001% increase in accuracy). Big-O notation allows for us to communicate the burden of an algorithm irrespective of its language specific implementation or the computers hardware.

This post will introduce the concepts of Big-O notation for general algorithms. A follow up post will discuss time complexity for common data science algorithms along with the additional challenges they entail (computational burden of cross validation and hyperparameter optimization).

## Big-O Notation

The notation used for Big-O always follows the same form; O(highest order of complexity). For example, if an algorithm has $T(n)=n^2 + 10n + 600$ we summarize this as $O(n^2)$ since as $n \rightarrow \infty$ both $10n$ and $600$ become negligible compared to $n^2$. We can explicitly show this by asking if $T(n)$ is $O(n^2)$ if $T(n) \leqslant cn^2$;

$n^2 + 10n+600 \leqslant cn^2 \implies 1+\frac{10}{n}+\frac{600}{n^2}\leqslant c$  

Since $T(n)\leqslant cn^2$ for some c, we have proven that $T(n)=O(n^2)$.

The equal sign when using Big-O notation can be misleading as it is a unidimensional operator.  It does not mean that all algorithms within a specific order are equivalent, just that they belong to that order. For example, say we have the algorithms $f(n) = O(2n^2 + 100) = O(n^2)$ and $g(n) = O(1000n^2 + 900n)=O(n^2)$. Both $f(n) = O(n^2)$ and $g(n) = O(n^2)$, however, $f(n) \ne g(x)$. It is easier to think of the equal sign in Big-O as meaning 'belongs to' instead of 'is', such as $f(n) \in O(n^2)$ and $g(x) \in O(n^2)$.

Often the complexity of an algorithm is also sensitive to the specific input as well as the length of the input, such as ordering a vector that is randomly scrambled versus one in reversed order. In these cases it can be useful to think of the different use cases that can occur, such as the worst case, average case, and best case. Generally we are most concerned with the average case and worst case, as those are the conditions we want to explicitly plan for.

## Orders of Time Complexity

There are a large number of different orders of time complexity, but for this post I am going to just cover some of the most commonly occurring ones. This plot shows the differences in complexity as the input grows for some of the major orders.

<img src="/images/BigO/complexity_by_class.png" width="100%" style="padding:2px"/>

In order to make this less abstract I am going to describe the intuition behind these orders, some examples of algorithms that belong to them, and plot their runtime as a function of input size to show what they may look like in practice. The plots are intended to aid in gaining an intuition of what these orders may mean, however, it is usually not meaningful to compare algorithms with different intents as has been done here, such as an ordering vs. arithmetic algorithm. All plots were run on simulated data in R and generated using `ggplot2`.

### $O(1)$ : Constant

Constant complexity algorithms do not change as a function of the size of the input. Some examples of this are selecting the first element of a vector, looking up the length of a vector in R, and printing an element.

<img src="/images/BigO/O1_examples.png" width="100%" style="padding:2px"/>

### $O(logn)$ : Logarithmic

The only algorithm I was able to find that runs in $O(logn)$ is a binary search on a pre-ordered vector. Binary search compares the middle element of the vector to the searched value; if it is less, the search continues on the half of the data under the median, if larger then the data above the median. This continues recursively until either the median matches the value or there are no more splits available.

### $O(n)$ : Linear

Algorithms in $O(n)$ are directly proportional to the size of the input. Addition, subtraction, and taking the mean are all in $O(n)$ since they require each piece of data to be touched once. Similarly, applying a comparison statement/boolean logic (X < 5, X > 1, X == "hello") and the maximum require every data to be handled.

<img src="/images/BigO/On_examples.png" width="100%" style="padding:2px"/>

### $O(nlogn)$ : Loglinear

Loglinear usually involves doing some form of repeated binary comparisons across n relationships. For example, if trying to find the number of unique elements in an ordered array containing all unique elements, we could go through the array each element at a time, adding each new one to a new list, and doing a binary search tree on the list of unique elements to see if the new element should be added to it. At worst (all unique elements), this algorithm would be $O(nlogn)$. Many sorting algorithms are loglinear on average and/or for their worst case as well, such as quick sort and merge sort.  

<img src="/images/BigO/Onlogn_examples.png" width="100%" style="padding:2px"/>

### $O(n^c)$ : Polynomial

Three examples of quadratic order algorithms are the linear algorithms listed earlier (comparison, maximum, mean) applied to a 2-dimensional matrix. Another example would be multiplication across two numbers with n-digits, which involves multiplication across all terms and then summing them up $(T(n) = c_0n^2+c_1n \in 0(n^2)).$

<img src="/images/BigO/Onc_examples.png" width="100%" style="padding:2px"/>

### $O(c^n)$ : Exponential

Recursive algorithms for calculating a Fibonacci sequence follow exponential time (as at each step two numbers must be added). Another example is searching a vector for two numbers which add up exactly to a value P.

### $O(n!)$ : Factorial

The simplest example of a factorial algorithm is calculating the factorial of an integer or calculating every possible combination of an input.
