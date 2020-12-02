---
title: "Building a good histogram Estimator"
date: 2020-11-17
tags: [Statistics, Histogram, Estimators]
header:
 image: "/images/pytorch.jpg"
excerpt: "Statistics, Histogram, Estimators"
mathjax: "true"
---

Let us take $(X_1, ..., X_n)$ an $n-$sample in $E = [0 , 1]$ with a density $p$ with respect to the Lebesgue measure $\lambda$. We will work in the case of a regular partition $\mathcal{I}$ of $E$ into $D$ equal sub-intervals.

We would like to estimate $p$, and in the course we have studied histogram estimators; The histogram of $p$ based on the partition $\mathcal{I}$ is the random function:
$$\hat{p}_{\mathcal{I}}(x) = \sum_{I \in \mathcal{I}} \frac{N_I}{n \lambda(I)} \mathds{1}_{I}(x)$$ where $N_I$ is 'number' of data which fall into $I$, more precisely: $N_I = \sum_{i=1}^n \mathds{1}_{X_i \in I}$. Since we have chosen a regular partition, we have in particular, for all $I \in \mathcal{I}, \lambda(I) = \frac{1}{D}$.

Finally, let us denote by $V_{\mathcal{I}}$ the linear space of step functions: $\sum_{I \in \mathcal{I}} a_{I} \mathds{1}_I$ where $a_I \in \mathbb{R}$. We recall the $L^1$-risk of the histogram as:
$$R_1 = \mathbb{E}_p(||p - p_{\mathcal{I}}||_1) $$ Moreover, we have the following bound (for regular partitions into $D$ intervals):

$$\mathbb{E}_p(||p - p_{\mathcal{I}}||_1) \leq 2\text{ }  \underset{f \in V_{\mathcal{I}}}{ inf} ||p - f||_1 + \sqrt{\frac{D-1}{n}}$$

Suppose we would like to find numerically a histogram $\bar{p}$ in $V_{\mathcal{I}}$ such that $||p - \bar{p}_D||_1$ is as small as possible. The classical approach (in case we know $p$) would be to compute for each choice of partition $\mathcal{I}$ (depending on $D$), the quantity $\int_{0}^1|p(x) - \hat{p}_D(x)|dx$ and therefore to deduce the value of $D$ which achieves this minimum.

### H3 Heading

Basic tex

$$x + y = \frac{2}{3} $$

![alt]({{ site.url }}{{ site.baseurl }}/images/me.jpg)
