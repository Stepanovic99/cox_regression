# Untitled
Stephan Voegele
2024-06-20

- [<span class="toc-section-number">1</span> Objectives](#objectives)
- [<span class="toc-section-number">2</span> Theoretical
  background](#theoretical-background)
  - [<span class="toc-section-number">2.1</span> Survival
    Function](#survival-function)
  - [<span class="toc-section-number">2.2</span> Hazard
    Function](#hazard-function)
- [References](#references)

# Objectives

- Provide theoretical background
- Apply Cox regression to dataset
- Check assumptions

Dataset: Prostate cancer dataset from Harrell et al. (2015)

- Response: Survival time
- Predictors: several

# Theoretical background

## Survival Function

## Hazard Function

Relative risks or are not applicable for survival times, because we
usually cannot assume that the risk is constant over time. We need to
create a different measure of risk which takes the time into account,
the `hazard ratio`. The central concept is the hazard function.

The idea behind the hazard function is to divide the time in consecutive
intervals

![\[0, t^{(1)}\], \[t^{(2)}, t^{(3)}\], \[t^{(q-1)}, t^{(q)}\]](https://latex.codecogs.com/svg.latex?%5B0%2C%20t%5E%7B%281%29%7D%5D%2C%20%5Bt%5E%7B%282%29%7D%2C%20t%5E%7B%283%29%7D%5D%2C%20%5Bt%5E%7B%28q-1%29%7D%2C%20t%5E%7B%28q%29%7D%5D "[0, t^{(1)}], [t^{(2)}, t^{(3)}], [t^{(q-1)}, t^{(q)}]")

The relative risk in the q-th interval is then defined as the quotient
of the conditional probability that a patient is still alive at the
beginning of the interval, for therapy A and B.

From this idea follows the hazard function:

![\lambda(t)=\lim\_{h \to 0}\frac{P(t\<T\<t+h\|T\>t)}{h}](https://latex.codecogs.com/svg.latex?%5Clambda%28t%29%3D%5Clim_%7Bh%20%5Cto%200%7D%5Cfrac%7BP%28t%3CT%3Ct%2Bh%7CT%3Et%29%7D%7Bh%7D "\lambda(t)=\lim_{h \to 0}\frac{P(t<T<t+h|T>t)}{h}")

<div>

> **Note**
>
> Therefore, the hazard function can be defined as the instantaneous
> probability of having an event at time t, given that one has survived
> up to time t.

</div>

# References

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-harrell2015regression" class="csl-entry">

Harrell, Frank E et al. 2015. *Regression Modeling Strategies: With
Applications to Linear Models, Logistic Regression, and Survival
Analysis*. Vol. 608. Springer.

</div>

</div>
