---
title: "Probabilistic generative models: an elementary model"
date: 2020-01-08
tags: [statistics, Naive Bayes, Maximum Likelihood Estimator, Likelihood]
header:
 image: 
excerpt: "Sampling from a distribution using Naive Bayes: helping the animal shelter prepare for the summer"
mathjax: "true"
---

Let us go back to the animal shelter we once helped in another article. Suppose we would now like to help that shelter better prepare for the next season:  how can we predict what the arriving animals might look like in order to be prepared (should we buy more cat or dog food this summer ? should we buy more grooming accessories for furry cats or more toys for dogs ?). 


![alt]({{ site.url }}{{ site.baseurl }}/figures2/groom.png)

This is equivalent to ask whether we can generate some new samples based on the ones we already have to 'create' some new, unseen animals. We would of course like these generated animals to be representative of reality, ie. to be somewhat similar to the ones we have already observed. 
I'm going to define these notions more rigorously using the statistical framework. Then, I will apply this to the already cleaned dataset of the shelter animal outcomes.


## Statistical framework
Let $$p_{\theta}(x)$$ be a family of density functions that can be described using a finite set of parameters $$\theta$$. For example, taking the gaussian densities would require us to specify $$\theta = (\mu ; \sigma)$$ to be uniquely specified. 

The likelihood $$L(\theta | x)$$ of $$\theta$$ is a function that measures the plausibility of $$\theta$$ given some observed data point $$x$$.
Now, if we consider the whole dataset $$X$$ we can define:

$$ L(\theta, X) = \Pi_{x \in X} p_{\theta}(x) $$


## 