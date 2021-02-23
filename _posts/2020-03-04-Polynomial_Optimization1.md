---
title: "Polynomial Optimization - Part I"
date: 2020-03-04
tags: [optimization, polynomials, Groebner Bases, SageMath]
header:
  teaser: "/figures3/polynomials.png"
excerpt: "Polynomial Optimization I: Solving multivariate polynomial systems. Why polynomials are cooler than I thought !"
mathjax: "true"
---


Polynomial functions are one of the first mathematical tools one encounters, and for good reason: they allow us to express elementary algebraic relationships between unknown quantities, using the four elementary operations (multiplication, addition, subtraction and (almost) division).
One might remember the quadratic formula that allows us to express exact solutions of a second degree polynomial $$P$$ of the form $$P(x) = ax^2 + bx + c$$:

$$x_{1, 2} = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$$ 

(of course, whenever real solutions exist and $$a \neq 0 $$)

The study of polynomials often stops here, for most people. In my undergraduate studies (in math), I don't remember giving polynomials much thought except when it came to abstract algebra (like Galois theory), or finding approximate roots to high degree polynomials (when a numerical approximation was sufficient, - hello Euler methods).

Studying polynomials outside the realms of abstract algebra or numerical approximation methods seemed like an unpopular opinion for a very long time. After all, why would anyone bother with the study of polynomials when many models in physics or biology use more sophisticated weapons, and when numerical methods allow us to compute whatever we want with arbitrary precision.

But what if polynomials had more to them than just that ? What if they could be of use in some practical machine learning problems ?


I would like to dedicate this article and the next one to the study of multivariate polynomials and to their applications in Data Science. Applying some ideas from the excellent book [Ideals, Varieties and Algorithms by Cox, Little and O'Shea](https://www.springer.com/gp/book/9783319167206), I'm first going to provide solutions to two sample problems by solving systems of multivariate polynomial equations. 


![alt]({{ site.url }}{{ site.baseurl }}/figures3/system.png)

![alt]({{ site.url }}{{ site.baseurl }}/figures3/triangle.png)



The aim of this 'Polynomial Optimization - Part I' article is to:
* Provide a very brief theoretical overview of the tools that can be used to solve multivariate polynomial systems
* Illustrate these tools by brute forcing two math Olympiad problems (I will be using the software SageMath for the computations)
and last but not least:
* Wonder how we could apply these tools to practical machine learning problems (in the next article, Polynomial Optimization - Part II !).


## Two Problems, one solution

### Solving difficult polynomial equations

### Finding the side of an equilateral triangle 



## Multivariate polynomial theory (summary)


## Problem solutions


### 








```python

```