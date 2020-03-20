---
layout: post
title: Goals in English Football Matches are Underdispersed
mathjax: yes
categories: [efl]
tags: [efl, model selection, poisson]
---

This post is a fairly in-the-weeds discovery from a project that I've been working on in my spare time for a while now. It's a nuanced statistical concept, and it probably won't make sense without the context of the project. But it's cool, so here we go.

## EFL modeling project

For a while I've been working on modeling the results of English soccer games to see if I can make good predictions of future games. One approach is to try to predict the actual score of each game.

## What is underdispersion?

If data are underdispersed, it means that their variance is smaller than we would expect based on some model. That's it.

But there's always more to the story. The number of goals scored by a team is an example of "count data". Count data is usually modeled by a Poisson distribution. Poisson distributions are ubiquitous in statistics, and they arise from just a few simple assumptions about the process generating your data (known as a "Poisson process"). So they make sense as a go-to distribution for counts. But Poisson distributions have the funny property that their variance is always equal to their mean. This is neat, mathematically, but practially can pose problems.

What if your data has a variance that is larger than its mean? That is called "overdispersion", and it means that a Poisson model might predict fewer extreme values than you would realistically see in actual measurements. What if your data has variance that is smaller than its mean? That is called "underdispersion", and it means that a Poisson model might predict more extreme values than you would realistically see in actual measurements.

## How do we know that goals are underdispersed?

## What can we do about it?

The most straightforward way to account for underdispersion is to use some other discrete distribution besides Poisson to model goal scoring. 

Unfortunately, the options there are kind of slim. Overdispersed data is a lot more common than underdispersed, so there aren't that many distributions that can result in underdispersed counts. Here are a few that I could find:
* Binomial distribution
* [Consul and Jain (1973)](https://www.jstor.org/stable/1267389) Generalized Poisson Distribution
* [Conway-Maxwell-Poisson](https://en.wikipedia.org/wiki/Conway%E2%80%93Maxwell%E2%80%93Poisson_distribution) Distribution
* A discretized gamma distribution: $$\lfloor X \rfloor$$ where $$X \sim gamma(\alpha, \beta)$$.

The binomial distribution requires setting a maximum allowable count, $$n$$, which doesn't fit the data. (Hypothetically, any number of goals could be scored in a game.) We could estimate $$n$$ but Stan doesn't handle discrete parameters well.

The Conway-Maxwell-Poisson distribution is conceptually pretty simple, but computing the mass function would require a normalizing constant with no closed form. The mean and variance also have no closed form, so the parameters aren't very interpretable.

The discretized gamma distribution is easier to understand. But using it might be difficult as the expected value and mean are only approximately equal to those of the underlying gamma.

So I'm going with the Consul and Jain distribution. 


