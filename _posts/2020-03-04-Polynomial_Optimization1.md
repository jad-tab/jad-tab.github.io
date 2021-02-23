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

(whenever real solutions exist and $$a \neq 0 $$)

The study of polynomials often stops here, for most people. In my undergraduate studies (in math), I don't remember giving polynomials much thought except when it came to abstract algebra (like Galois theory), or finding approximate roots to high degree polynomials (when a numerical approximation was sufficient, - hello Euler methods).

Studying polynomials outside the realms of abstract algebra or numerical approximation methods seemed like an unpopular opinion for a very long time. After all, why would anyone bother with the study of polynomials when many models in physics or biology use more sophisticated weapons, and when numerical methods allow us to compute whatever we want with arbitrary precision.

But what if polynomials had more to them than just that ? What if they could be of use in some practical problems, in machine learning ? Or at least, problems like finding the sides of a room, given the distance of a person to its three vertices:

![alt]({{ site.url }}{{ site.baseurl }}/figures3/triangle.png)

*Source: Ireland National Olympiads (High School)*

I would like to dedicate this article and the next one to the study of multivariate polynomials and to their applications in Data Science/Machine Learning. Applying some ideas from the excellent book [Ideals, Varieties and Algorithms by Cox, Little and O'Shea](https://www.springer.com/gp/book/9783319167206), I'm going to provide solutions to two sample problems by solving systems of multivariate polynomial equations, systems that look like this one:


![alt]({{ site.url }}{{ site.baseurl }}/figures3/system.png)

*Source: 'Mathematical Reflections - two wonderful years' by Titu Andreescu, Maxim Ignatiuc*

The aim of this 'Polynomial Optimization - Part I' article is therefore to:
* Provide a very brief theoretical overview of the tools that can be used to solve multivariate polynomial systems
* Illustrate these tools by brute forcing two math Olympiad problems (I will be using the software SageMath for the computations)
and last but not least:
* Wonder how we could apply these tools to real-world, practical machine learning problems (in the next article, Polynomial Optimization - Part II !).



## Multivariate polynomial theory (summary)

This section is my modest attempt at sketching and summarizing a whole theory  - I apologize in advance to any algebraists who might not appreciate it. Please refer to the book above for more rigor and details.

### Analogy with linear algebra and critical differences
Let us consider the following system of equations:


$$\begin{cases}
  2x + 3y = 16 \\ 3x + y = 17
  \end{cases}$$
  
The reason why this system of polynomial equations might be difficult to solve is the presence of two unknowns $$x$$ and $$y$$ that are intertwined. A practical way to solve it would be to express $$y$$ in terms of $$x$$ in the second equation and plugging it into the first one. 
While that is quite easy to do here, it seems to be more difficult in the following case:

$$\begin{cases}
  x^3 - 2xy = 0 \\ x^2y - 2y^2 + x = 0
  \end{cases}$$


The theory behind polynomials allows us to transform the above system of equations into the following, equivalent system:

 $$\begin{cases}
y^2 - \frac{x}{2} = 0 \\ xy = 0  \\ x^2 = 0
\end{cases}$$

Much easier right ! Namely, because the final equation only depends on $$x$$. That allows us to determine $$y$$ by substitution, just like in linear algebra.
While it is quite difficult to expose a whole theory here, I'd like to briefly state the main challenges we encounter.


* The ring $$\mathbb{K}[X_1, ..., X_n]$$ of multivariate polynomials is not a Euclidean domain (as opposed to $$\mathbb{K}[X]$$: it is less straightforward to define the critical notion of 'polynomial division')
  
* A system of multivariate polynomials can be interpreted as an ideal $$I$$ of the ring of multivariate polynomials
  
* A very valuable tool to find a "suitable" base of $$I$$: Groebner bases  (as in the system above, a suitable base is one that makes the system easier to solve)
  
* There exist some computational algorithms that can effectively transform any given system into another simpler one that is equivalent (Buchberger's algorithm) 

* Choosing the lexicographic monomial ordering is a good way to obtain the value of one of the unknowns in the system

* Some systems can have an infinite number of solutions, or no solutions at all: that is the notion of a variety's dimensions - which is outside the scope of this article.



## One-hit KO'ing problems with multivariate polynomials

### The triangle 

Consider the following equilateral triangle: 

![alt]({{ site.url }}{{ site.baseurl }}/figures3/triangle.png)
Given that MC = 3, MA = 4 and MB = 5, compute the side of the triangle. 

Other variants of this problem exist, I've seen one [here](https://mindyourdecisions.com/blog/2019/09/19/area-of-the-square-puzzle-olympiad-practice/)





#### Putting into polynomial equations


#### A trick to formulate inequality bounds

### The system



## Next steps: ways to apply these tools to the real world ?




## Bonus content: more subtle solutions for the problems !











```python

```