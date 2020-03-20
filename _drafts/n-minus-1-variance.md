---
layout: post
title: Why Do We Divide By n-1 When Calculating Variance?
mathjax: yes
categories: [introstat]
tags: [variance, bias, inference]
---

This is a question that I get a lot from students during the first week of class, and it's surprisingly hard to answer. There are two reasons I have such a hard time answering it:

1. The explanation depends on concepts that we haven't covered yet in class, or that we won't cover, and
2. I'm never really satisfied with the answer. There's no good place to stop the explanation without leaving more questions unanswered.

What I want to do here is through a series of posts, answer the $$n-1$$ question as well as I can at a level that a student halfway through an intro statistics class can understand.

### Background

The formula for sample variance that we teach in an introductory statistics class is

$$s^2 = \frac{1}{n-1}\sum_{i=1}^n (x_i - \bar{x})^2.$$

We commonly describe this as a kind of "average squared distance" from the mean. One student always asks why we divide by $$n-1$$ and not $$n$$ if this is supposed to be an average of our $$n$$ data points. I always just have to give some vague answer about degrees of freedom or bias, and I feel like I've just made things worse.

## Part 1: Bias

To understand bias, we need to think about why we want to calculate variance in the first place. Sample variance is a *descriptive* statistic, meaning it describes a sample, but it can also be used for *inference*. In other words, we would like the sample variance to be a pretty good estimate of the variance of the population from which our sample came.

### Simulation

### Proof

If we can show that the expected value of $$SS$$ is $$(n-1)\sigma^2$$, then we will know that $$s^2 = \frac{SS}{n-1}$$ is unbiased. First, let's rework the formula for $$SS$$ a little bit into something that will be easier to work with:

$$\begin{align*}
SS &= \sum_{i=1}^n (x_i - \bar{x})^2 \\
&= \sum_{i=1}^n (x_i^2 - 2x_i\bar{x} + \bar{x}^2) 
    \color{blue}\text{ ,  by distributing the square} \\
&= \sum_{i=1}^n x_i^2 - 2\bar{x}\sum_{i=1}^n x_i + n\bar{x}^2 
    \color{blue}\text{ ,  by distributing the sum} \\
&= \sum_{i=1}^n x_i^2 - 2n\bar{x}^2 + n\bar{x}^2 
    \color{blue}\text{ ,  since $\sum_{i=1}^n x_i = n\bar{x}$} \\
&= \sum_{i=1}^n x_i^2 - n\bar{x}^2
\end{align*}$$

Now, finding $$E[SS]$$ simplifies to

$$\begin{align*}
E[SS] &= E\left[\sum_{i=1}^n x_i^2 - n\bar{x}^2\right] \\
&= \sum_{i=1}^n E[x_i^2] - nE[\bar{x}^2]
    \color{blue}\text{ ,  by distributing the expected value} \\
&= nE[x_1^2] - nE[\bar{x}^2]
    \color{blue}\text{ ,  since all $x_i$ are distributed identically}
\end{align*}.$$

All we need to know now are $$E[x_1^2]$$ and $$E[\bar{x}^2]$$, and we will be able to find $$E[SS]$$. To do this, I'll use the fact that for a random variable $$Z$$, $$Var(Z) = E[Z^2] - E[Z]^2$$. We can rearrange this to get $$E[Z^2] = Var(Z) + E[Z]^2$$.

Since $$E[x_1] = \mu$$, $$Var(x_1) = \sigma^2$$, $$E[\bar{x}] = \mu$$, and $$Var(\bar{x}) = \frac{\sigma^2}{n}$$,

$$\begin{align*}
E[SS] &= nE[x_1^2] - nE[\bar{x}^2] \\
&= n(\sigma^2 + \mu^2) - n(\frac{\sigma^2}{n} + \mu^2) \\
&= (n-1)\sigma^2
\end{align*}$$

Therefore the estimator $$s^2$$ is unbiased for $$\sigma^2$$ because $$E[s^2] = \sigma^2$$.
