# Using Input Cardinalities To Improve Set Intersection Estimation

## Abstract 

A fundamental problem in data sketches is accurately estimating the cardinality of the union or intersection of sampled sets. Sampling bias in the inputs produces bias in the output. Ting has proposed combining component estimators to compensate for each source of bias, using partial information about cardinality of each component, and weighting each component separately.

We explore here a slightly simpler problem of how to correct the output bias from multiple inputs, if given their true cardinalities. In this case we produce a single correction factor that combines the input components at lower levels.  A straightforward weighting scheme based on Least Squares produces a correction factor that reduces variance in the estimate.

This technique extends to an arbitrarily large number of operands as a closest-point problem on a convex hull, which may be easier to solve than other methods.

## Background

### Theta Sampling
Theta Sketches use a sample of the data taken with coordinated hashing, where a single hash function is applied to all sets being sketched, and items which fall below a setwise threshold $\theta$ are retained:

$h(S,\theta) = \{ x \in S: hash(x) < \theta \} $

The Theta estimator 

$\hat{N}(S,\theta) = \frac{|h(S,\theta)|}{\theta}$

is an unbiased estimator of S's cardinality. This estimate differs by a factor $|h(S,\theta)|/n = C$ from the true value, due to variance in the hash function.

To estimate C for the intersection or union of two set sketches, we will pursue best estimates for the numerator and denominator.

### Vector Embedding

Given a set $S$ of integers drawn from a range $\{1..N\}$, we can represent it as a vector $\vec S = (s_i) \in \mathbb{R}^N$ st $ s_i = 1 \iff i \in S $ and 0 otherwise. 

This embedding has the property that $\langle \vec S,\vec S \rangle = \vert S \vert$ and $\langle \vec S, \vec T \rangle = \vert S \cap T \vert$.


## Weighted Averaging

Weighted averaging allows us to reduce the "signal-to-noise" ratio of the average vs. either input, where the "signal" is the intersection, and the "noise" are two uncorrelated difference vectors.

Given two sets $S_i$ and $I = S_1 \cap S_2$ we can write them vectorwise as $ \vec S_i = \vec I + \vec R_i$ with $\vec R_1 \perp \vec R_2 $. We wish to find the weighted average of $ \vec S_i $ which minimizes distance to $\vec I$.

If $w_i$ are nonnegative weights summing to 1, the locus of weighted sums $w_1 \vec S_1 + w_2 \vec S_2$ is the hypotenuse of a right triangle with vertices $\vec I, \vec S_1, \vec S_2$, having its closest (Least Squares) point to $\vec I$ at $w_i \propto 1/||R_i||^2$.


## Weighted Correction

To estimate the ratio between the intersection true cardinality and the sampled version, we estimate each with the weighted averaging method shown above, from the following inputs:

||<div style="width:100px">$| S_i |$ </div> |  <div style="width:100px">$|S_1 \cap S_2 | $ </div>|
|---|------|--------------------|
|Numerator| exact|$\hat{N}(S_1 \cap S_2,\theta)$|
|Denominator| $\hat{N}(S_1, \theta)$|$\hat{N}(S_1 \cap S_2,\theta)$|


