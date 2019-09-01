---
layout: post
title: Why Do We Divide By n-1 When Calculating Variance?
mathjax: yes
categories: [introstat]
tags: [variance, bias, inference]
---

This is a question that I get a lot from students during the first week of
class, and it's surprisingly hard to answer to my own satisfaction. The
reason I have a hard time answering it is because wherever I stop, there's
always another "why?". Through a series of posts, I want to play the "why"
game as long as I can: start with simple posts and work up to the complex but
more complete answers.

## Background

What do I mean, "divide by $n-1$"? The formula for sample variance that we
teach in introductory statistics classes is
$$s^2 = {{1} \over {n-1}}\sum_{i=1}^n (x_i - \bar{x})^2.$$
We commonly describe this as a kind of "average squared distance" from the
mean, after which students ask why we divide by $n-1$ and not $n$ if this is
supposed to be an average. This is a really good question.