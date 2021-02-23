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
Given that MC = 3, MA = 4 and MB = 5, compute the side c of the triangle. 

I've seen other variants of this problem, like the one [here](https://mindyourdecisions.com/blog/2019/09/19/area-of-the-square-puzzle-olympiad-practice/)
![alt]({{ site.url }}{{ site.baseurl }}/figures3/polynomialmin.png)

Five minutes ? Challenge accepted !

#### Putting into polynomial equations

So back to the triangle, a very fast (but not very elegant) way is to write that the sum of the areas of the three inner triangles (AMC, AMB, BMC) is equal to the area of the whole equilateral triangle ABC. 

The area of the equilateral triangle with side $$c$$ is of course $$\frac{\sqrt{3}}{4} c^2 $$
The areas of the small triangles can quickly be expressed using Heron's formula that links the sides and the area of any given triangle.

For instance, if we denote by p the perimeter of a triangle (with sides a,b, and c) divided by two, and A its area, then we have:

$$ \sqrt{p(p-a)(p-b)(p-c)}= A $$

For instance, the area of AMC is given by: 

$$AMC = \sqrt{p(p - 3)(p - 4)(p - c)}$$

where $$p = \frac{c + 7}{2}$$


Let us denote by u the area of AMC, by v the area of BMC, and by w the area of AMB.
Setting the areas to be equal we obtain the following equation:

$$\sqrt{(c/2 + 7/2)(c/2 + 7/2 - 3)(c/2 + 7/2 - 4)(c/2 + 7/2 - c)} \\ + 
\sqrt{(c/2 + 4)(c/2 + 4 - 3)(c/2 + 4 - 5)(c/2 + 4 - c)} \\ + 
\sqrt{(c/2 + 9/2)(c/2+9/2-4)(c/2 + 9/2 - 5)(c/2 + 9/2 - c)}  \\ =
\frac{\sqrt{3}}{4} c^2$$

Since that equation is not polynomial, a nice way to fall back on our feet is by introducing $$u, v, w$$ the expressions of the areas of each small triangle. 
One final step would be to add the equation  $$16x^2 - 3 = 0$$ and replace $$\frac{\sqrt{3}}{4}$$ by $$x$$ (the reason for this is a bit theoretical: to allow all computations to be done formally in $$Q$$).

We therefore have the equivalent formulation of our problem: 

 $$\begin{cases}
u + v + w = \frac{\sqrt{3}}{4} c^2 \\ u^2 = (c/2 + 7/2)(c/2 + 7/2 - 3)(c/2 + 7/2 - 4)(c/2 + 7/2 - c) \\v^2 = (c/2 + 4)(c/2 + 4 - 3)(c/2 + 4 - 5)(c/2 + 4 - c)\\ w^2 = (c/2 + 9/2)(c/2+9/2-4)(c/2 + 9/2 - 5)(c/2 + 9/2 - c) \\ 16x^2 - 3 = 0
\end{cases}$$

This system is quite difficult to solve as is, so we will use the untangling method described above via SageMath.
I start by defining the variables and the polynomials that define an ideal of the polynomial ring $$Q[u,v,w,x,c]$$. I choose the lexicographic monomial ordering.

```python
var('c u v w x')

p1 = u^2 - (c/2 + 7/2)*(c/2 + 7/2 - 3)*(c/2 + 7/2 - 4)*(c/2 + 7/2 - c)
p2 = v^2 - (c/2 + 4)*(c/2 + 4 - 3)*(c/2 + 4 - 5)*(c/2 + 4 - c)
p3 = w^2 - (c/2 + 9/2)*(c/2+9/2-4)*(c/2 + 9/2 - 5)*(c/2 + 9/2 - c)
p4 = u + v + w - x*c^2
p5 = 16*x^2 - 3


K.<u,v,w,x,c> = PolynomialRing(QQ, 5, order='lex')
I = (p1, p2, p3, p4,p5)*K
```

Then I compute a Groebner basis of I. It looks something like this:

```python
G = I.groebner_basis()
G
︡98f6e414-2b85-4999-803b-89bce93afa66︡{"stdout":"[u - 7/9*w - 961/31144824*x*c^12 + 30625/10381608*x*c^10 - 4488125/46717236*x*c^8 + 20959225/15572412*x*c^6 - 255681985/31144824*x*c^4 + 1897921153/93434472*x*c^2, v + 16/9*w + 961/31144824*x*c^12 - 30625/10381608*x*c^10 + 4488125/46717236*x*c^8 - 20959225/15572412*x*c^6 + 255681985/31144824*x*c^4 - 1991355625/93434472*x*c^2, w^2 + 1/16*c^4 - 41/8*c^2 + 81/16, w*c^2 + 137/3491712*x*c^12 - 703/166272*x*c^10 + 858253/5237568*x*c^8 - 1653347/581952*x*c^6 + 8027841/387968*x*c^4 - 595034393/10475136*x*c^2, x^2 - 3/16, c^14 - 100*c^12 + 10579/3*c^10 - 170800/3*c^8 + 458787*c^6 - 5820100/3*c^4 + 9912673/3*c^2]


(c^14 - 100*c^12 + 10579/3*c^10 - 170800/3*c^8 + 458787*c^6 - 5820100/3*c^4 + 9912673/3*c^2).factor()
```

A bit scary, but notice how the final equation depends only on one variable, c ! 
The final step is achieving the factorization to solve an equation in c. We obtain nine real solutions, but the one that works here is 
$$ c = \sqrt(25 + 12 sqrt(3)) $$



#### A trick to formulate inequality bounds

### The system



## Next steps: ways to apply these tools to the real world ?




## Bonus content: more subtle solutions for the problems !









