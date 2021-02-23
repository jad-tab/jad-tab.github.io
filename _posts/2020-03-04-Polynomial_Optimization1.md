---
title: "Polynomial Optimization - Part I"
date: 2020-03-04
tags: [optimization, polynomials, Groebner Bases]
header:
  teaser: "/figures/guskooter.png"
excerpt: "Polynomial Optimization I: Solving multivariate polynomial systems"
mathjax: "true"
---


Polynomial functions are one of the first mathematical tools one encounters, and for good reason: they allow us to express elementary algebraic relationships between unknown quantities. 
One might remember the quadratic formula that allows us to express exact solutions of a second degree polynomial $$P$$ of the form $$P(x) = ax^2 + bx + c$$ 
$$x_{1, 2} = \frac{-b +- \sqrt{b^2 - 4ac}}{2a}$$ (of course, whenever real solutions exist).

The study of polynomials often stops here, for the general students. In my undergraduate studies, I don't remember giving polynomials much thought except when it came to abstract algebra (like Galois theory), or finding approximate roots to high degree polynomials when a numerical approximation was sufficient.

In this article, I would like to 
![alt]({{ site.url }}{{ site.baseurl }}/figures/guskooter.png)

 

Using a dataset from the [Shelter Animal Outcomes (Kaggle Competition)](https://www.kaggle.com/c/shelter-animal-outcomes/overview), I'm going to build predictive models to predict the animals' destinies. Though the dataset comes from a Kaggle competition, I'm not focusing here on building the most accurate model possible. Instead, I will take my time preprocessing the data and cleaning it: as we're going to see, the real world data isn't always very tidy and requires some extra steps. 


 
* $$M = 5$$ (number of outcome classes), 
* $$y_{ij} = 1$$ (if observation $$i$$ is in outcome $$j$$), $$y_{ij} = 0$$ (if observation $$i$$ is not in outcome $$j$$).





```python

```

![alt]({{ site.url }}{{ site.baseurl }}/figures/algo1.PNG)


## Dropping some features, converting some others 