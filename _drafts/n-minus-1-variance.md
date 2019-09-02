---
layout: post
title: Why Do We Divide By n-1 When Calculating Variance?
mathjax: yes
categories: [introstat]
tags: [variance, bias, inference]
---

This is a question that I get a lot from students during the first week of
class, and it's surprisingly hard to answer. There are two reasons I have such
a hard time answering it:

1. The explanation depends on concepts that we haven't covered yet in class, or that we won't cover, and
2. I'm never really satisfied with the answer. There's no good place to stop the explanation without leaving more questions unanswered.

What I want to do here is through a series of posts, answer the $$n-1$$
question as well as I can at a level that a student halfway through an intro
statistics class can understand.

### Background

The formula for sample variance that we teach in an introductory statistics 
class is

$$s^2 = \frac{1}{n-1}\sum_{i=1}^n (x_i - \bar{x})^2.$$

We commonly describe this as a kind of "average squared distance" from the
mean. One student always asks why we divide by $$n-1$$ and not $$n$$ if this is
supposed to be an average of our $$n$$ data points. I always just have to give 
some vague answer about degrees of freedom or bias, and I feel like I've just
made things worse.

## Part 1: Bias

To understand bias, we need to think about why we want to calculate variance in
the first place. Sample variance is a *descriptive* statistic, meaning it
describes a sample, but it can also be used for *inference*. In other words, we
would like the sample variance to be a pretty good estimate of the variance of
the population from which our sample came.

### Simulation

### Proof
