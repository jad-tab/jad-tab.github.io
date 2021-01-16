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

This is equivalent to ask whether we can generate some new samples based on the ones we already have to 'create' some new, unseen animals. We would of course like these generated animals to be representative of reality ie. to be somewhat similar to the ones we have already observed, so not completely random. 
I'm going to define these notions more rigorously using the statistical framework. Then, I will apply this to the already cleaned dataset of the shelter animal outcomes.


## Statistical framework for parametric estimation

Let $$p_{\theta}(x)$$ be a family of density functions that can be described using a finite set of parameters $$\theta$$. For example, taking the gaussian densities would require us to specify $$\theta = (\mu ; \sigma)$$ to be uniquely specified. 

The likelihood $$L(\theta | x)$$ of $$\theta$$ is a function that measures the plausibility of $$\theta$$ given some observed data point $$x$$.
Of course, it makes sense to define it as:
$$L(\theta, x) = p_{\theta}(x) $$

Now, if we consider the whole dataset $$X$$ we can define:
$$ L(\theta, X) = \Pi_{x \in X}   p_{\theta}(x) $$

The interesting bit is when we consider $$\theta$$ for which $$L(\theta, X)$$ is maximum. That is precisely the definition of the maximum likelihood estimator $$\hat{\theta}$$:

$$\hat{\theta} = argmax_{\theta}  L(\theta, X) $$




Since we would like the model to give more weight to areas of the sample space that it believes are more likely, rather than just giving equal weight to point $$x$$ we've observed, we could try the Naive Bayes approach.

## Naive Bayes

The Naive Bayes parametric model assumes (naively) that each feature $$x_j$$ is independent from all other features $$x'_j$$. 


p(x_j | x_k) = p(x_j) 



