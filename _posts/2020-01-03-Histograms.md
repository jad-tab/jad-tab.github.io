---
title: "Building a good histogram estimator"
date: 2020-11-17
tags: [Statistics, Histogram, Estimators]
header:
 image: 
excerpt: "Statistics, Histogram, Estimators"
mathjax: "true"
---

Histograms are a cool way to visualize data distributions:


![alt]({{ site.url }}{{ site.baseurl }}/figures/histogram.JPG)

We could choose how we want our histogram to be plotted to draw insight from the data at hand. But how many bins should we use for a histogram in practice ?

In this article, I experiment with a procedure described by Birgé and Rozenholc that allows us to select a good number of bins. 

## Notations and mathematical context

Let us take $$(X_1, ..., X_n)$$ an $$n-$$sample in a space $$E = [0 , 1]$$. Let us suppose they are sampled from some density $$p$$ with respect to the Lebesgue measure $$\lambda$$. I will work in the case of a regular partition $$\mathcal{I}$$ of $$E$$ into $$D$$ equal sub-intervals. This $$D$$ variable is the number of bins we would like to use, in practice.

We would like to estimate $$p$$ using a histogram. The histogram of $$p$$ based on the partition $$\mathcal{I}$$ is the random function:
	
$$\hat{p}_{\mathcal{I}}(x) = \sum_{I \in \mathcal{I}} \frac{N_I}{n \lambda(I)} 1_{I}(x)$$ 

where $$N_I$$ is 'number' of data which fall into $$I$$, more precisely: $$N_I = \sum_{i=1}^n 1_{X_i \in I}$$. Since we have chosen a regular partition, we have in particular, for all $$I \in \mathcal{I}, \lambda(I) = \frac{1}{D}$$. 

Finally, let us denote by $$V_{\mathcal{I}}$$ the linear space of step functions: $$\sum_{I \in \mathcal{I}} a_{I} 1_I$$ where $$a_I \in \mathbb{R}$$. 

Let's remember that the $$L^1$$-risk of the histogram is defined by:
$$R_1 = \mathbb{E}_p(||p - p_{\mathcal{I}}||_1) $$ 

Moreover, we can establish the following bound (for regular partitions into $$D$$ intervals):

$$\mathbb{E}_p(||p - p_{\mathcal{I}}||_1) \leq 2\text{ }  \underset{f \in V_{\mathcal{I}}}{ inf} ||p - f||_1 + \sqrt{\frac{D-1}{n}}$$

in which the infimum is taken over all $$f \in V_{\mathcal{I}}$$.

Suppose we would like to find a good histogram $$\bar{p}$$ in $$V_{\mathcal{I}}$$ such that $$||p - \bar{p}_D||_1$$ is as small as possible. The classical approach (in case we know $$p$$) would be to compute for each choice of partition $$\mathcal{I}$$ (depending on $$D$$), the quantity $$\int_{0}^1|p(x) - \hat{p}_D(x)|dx$$ and therefore to deduce the value of $$D$$ which achieves this minimum. 
This seems a bit expensive, computationally...

If we look at the previous bound, we see that  there has to be some kind of tradeoff between the bias and the variance: just choosing the largest value of $$D$$ is ... too easy to be true. 


At this point, one can argue: "But ! I already know a rule of thumb to make this choice like the Sturge's rule which suggests to take a number of bins close to $$log_2(50) + 1$$ ! What's the point of this article!".
You're right ! If you're in a rush, go ahead. But experimenting with histograms can be fun.

Sturge's rule is a bit too general and depends only on the sample size and not on the particular distribution of the $$n$$-sample. The point of the method described here is to attempt to define a reasonable $$D$$ using information from the data.

Let me now describe briefly the methodology I have followed and the attempted examples. Let's dive in !


## Methodology


For all $$D \geq 1$$, let's define the log-likelihood at $$\hat{p}_D$$ based on the observations $$\mathbb{X} = (X_1, .... X_n)$$ by: $$L_{\mathbb{X}}(D) = \underset{I \in I_D}{\sum} N_I log(N_I \frac{ D }{n})$$

The heart of the procedure is to introduce a penalty function, defined for each integer $$D>0$$ as: 
$$pen(D) = D - 1 + (log(D))^{2.5}$$. The method suggests that the number of bins $$\hat{D}$$ to choose is: 

$$\boxed{\hat{D} = \underset{1 \leq D \leq n/log(n)}{argmax} (\sum_{I \in \mathcal{I_D}} N_I log(N_I D/n) - (D - 1 + (log(D)^{2.5})}$$

For the sake of this experiment, I will use a few mock probability densities for the $$n$$-sample.
Therefore, for any given density $$p$$, I'll first compute the $$\hat{D}$$ above (the one suggested by the procedure) and I'll then compare it with the value of $$D$$ which achieves $$M = \underset{1 \leq D \leq n/log(n)}{min} \int_0^1 \lvert p(x) - \hat{p}_D(x) \rvert dx$$. This will allow me to assess how this procedure measures up heuristically. Practically, if $$R$$:

$$R = \frac{\int_0^1|p(x) - \hat{p}_{\hat{D}}(x)|dx}{M}$$

... isclose to 1, this would mean that $\hat{D} \approx D_{min}$ (the procedure finds something close to the exhaustive search). 

For each density I try out, I'll compute the mean value and variance of the ratios obtained:

$$\bar{R} = \frac{1}{k} \sum_{j=1}^k R_j \quad \quad \bar{V} = \frac{1}{k} \sum_{j=1}^k (R_j - \bar{R})^2$$.

Hopefully, this will "smooth out" the effects of randomness.



## Generating an $n$-sample and choice of densities

To generate an $$n$$-sample with density $p$ I'm going to apply the fundamental theorem of simulation. Nothing too fancy.
There are of course much wittier ways to do that (Monte-Carlo rejection sampling...). But let's have some fun inverting functions. 
If I denote by $$U$$ a random variable with uniform distribution on $$[0 ; 1]$$, we have the following result:


### [Theorem: Simulation of an n-sample]

Let $$X$$ be a real-valued random variable with cumulative distribution function $$F(x) = \mathbb{P}(X \leq x)$$, we denote by $$F^{-1}$$ its generalized inverse defined as $$F^{-1}(u) = inf \{x : F(x) \geq u\}$$. We know that $$F^{-1}(U)$$ has the same distribution as $$X$$.

Therefore, in order to generate an $$n$$-sample, it is sufficient to sample from a uniform random distribution and to invert the cumulative distribution function of each of the distributions we consider. Let me first show you the distributions I have chosen to try out. 

$$p_1 = \frac{1}{2} 1_{[0, 1/2)} + \frac{3}{2} \times 1_{[1/2 ; 1]} \\
p_2 = 2 \times 1_{[0, 1/8]} + \frac{4}{5} \times 1_{[1/8 ; 3/4)} + 1_{[3/4; 1]} \\
p_3 = \frac{1}{Arctan(1) (1 + x^2)} 1_{[0,1]} $$

$$p_4(x) = 2(3x - floor(3x)) 1_{[0,1]}\\
p_5(x) = \frac{\pi}{3} sin(2 \pi x)1_{[0,1/2]} + \frac{2 \pi}{3} sin(2\pi (x - \frac{1}{2})) 1_{[1/2,1]}$$.
 
I didn't choose these densities (completely) at 'random', though (no pun intended). To be honest, I was trying to find ways to make the $$L^1$$ risk as bad as possible, using this inequality:

$$\underset{f \in V_{\mathcal{I}}}{inf} \lvert \lvert p - f \rvert \rvert _1 \leq \frac{1}{2D} V_{[0,1]}(p)$$