---
title: "Polynomial Optimization - Part I"
date: 2020-03-04
tags: [optimization, polynomials, Groebner Bases]
header:
  teaser: "/figures/guskooter.png"
excerpt: "Polynomial Optimization I: Solving multivariate polynomial systems. Why polynomials are cooler than I thought !"
mathjax: "true"
---


Polynomial functions are one of the first mathematical tools one encounters, and for good reason: they allow us to express elementary algebraic relationships between unknown quantities, using the four elementary operations (multiplication, addition, subtraction and (almost) division).
One might remember the quadratic formula that allows us to express exact solutions of a second degree polynomial $$P$$ of the form $$P(x) = ax^2 + bx + c$$:

$$x_{1, 2} = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$$ 

(of course, whenever real solutions exist and $$a \neq 0 $$)

The study of polynomials often stops here, for most people. In my undergraduate studies (in math), I don't remember giving polynomials much thought except when it came to abstract algebra (like Galois theory), or finding approximate roots to high degree polynomials (when a numerical approximation was sufficient, - hello Euler method !).

Studying polynomials outside the realms of abstract algebra or numerical approximation methods seemed like an unpopular opinion to me, for a very long time. After all, why would anyone bother with the study of polynomials if many models in physics or biology used more sophisticated weapons ?

But what if polynomials had more to them than just that ? What if they could be of use in some practical machine learning problems?

I would like to dedicate this article and the next one to the study of polynomials. Making use of [Shelter Animal Outcomes (Kaggle Competition)](https://www.kaggle.com/c/shelter-animal-outcomes/overview), I'm going to provide a solution to a mock geometry problem  


 
* $$M = 5$$ (number of outcome classes), 
* $$y_{ij} = 1$$ (if observation $$i$$ is in outcome $$j$$), $$y_{ij} = 0$$ (if observation $$i$$ is not in outcome $$j$$).





```python

```

![alt]({{ site.url }}{{ site.baseurl }}/figures/algo1.PNG)


## Dropping some features, converting some others 