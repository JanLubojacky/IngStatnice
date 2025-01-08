# SAN
> lineární regrese (předpoklady, odhad parametrů modelu, interpretace modelu, výběr relevantních proměnných, statistické srovnání mezi modely), nelineární regrese (příklady konkrétních algoritmů a jejich srovnání), analýza rozptylu, lineární a nelineární metody redukce dimenze (cíle, příklady konkrétních algoritmů, jejich vlastnosti a srovnání), shlukování (definice úlohy a její složitost, příklady hierarchických a nehierarchických metod shlukování, spektrální shlukování), robustní statistika (motivace pro robustní statistiku a příklady metod)

## Basics
**Probability basics**
- event $A$ is a member of the sample space $\Omega$, i.e. $A \in \Omega$
- probability $P(A)$ is a function that maps the sample space onto real numbers $P: \Omega \to \mathbb{R}$
- random variable is a mapping from the sample space to the real numbers $X: \Omega \to \mathbb{R}$

**Conditional probability**
$$P(A|B)=\frac{P(A\cap B)}{P(B)}$$
- $P(A\cap B)$ is the joint probability of $A$ and $B$

**Bayes Theorem**
$$P(A|B)=\frac{P(B|A)P(A)}{P(B)}=\frac{P(B|A)P(A)}{\int P(B|A)P(A)\:dA}$$
- where
  - $P(A|B)$ is the posterior (or conditional) probability of $A$ given $B$
  - $P(B|A)$ is the reverse, the conditional probability of $B$ given $A$
  - $P(A)$ and $P(B)$ are the is the *prior* or *marginal* probabilities of $A$ and $B$, i.e. the probability of observing them without any conditions


**Mean, Variance and Standard Deviation**
- sum is replaced by an integral for continous distributions

$$\textcolor{#98d8d6}{\mu} = E[X] = \sum_x P(X=x)\cdot x$$

$$\text{Var}(X) = E[(X - \textcolor{#98d8d6}{\mu})^2] = \sum_x P(X=x)\cdot(x - \textcolor{#98d8d6}{\mu})^2 $$

$$\sigma = \sqrt{\text{Var}(X)}$$

**Central limit theorem**
- The scaled sum $\sum_n X_n$ of realizations of i.i.d. random variables $X_i$ tends toward a normal distribution for arbitrary distributions of $X_i$
- Conditions
  - all $X_i$ are i.i.d. (independent and identically distributed)
  - that distribution has a finite variance

$$\lim_{N \to \infty} P(a < \frac{(X_1 + \cdots + X_N) - N \cdot \mu}{\sigma \cdot \sqrt{N}} < b) = \int_a^b \frac{1}{\sqrt{2\pi}}e^{-x^2/2}dx$$

**Hypothesis testing**
- $H_0$ null hypothesis, which we can either reject or not reject (such as $A$ and $B$ come from the same distribution, coefficient $\beta_0$ is not significant)
- $H_1$ alternative hypothesis, when $H_0$ is rejected (such as $A$ and $B$ come from different distributions, coefficient $\beta_0$ is significant)

**P-value**
- we have a statistical test, that produces test statistics (real numbers), these values are functions of the data being tested and as a result random variables that can be modelled with a distribution (such as a normal one, student-t, $\chi^2$-distribution, F-distribution)
- p-values are quantiles of this distribution given that the $H_0$ is true (i.e. no significance)
- a p-value of $0.05$ means that the test will produce such values $5\%$ of the time given that the $H_0$ is true, so if we set a p-value of $0.05$ for a statistical test we are saying that we want to be at least $95\%$ confident in the result before we trust it

**Type I and Type II errors**
- type I is false positive, we falsely reject $H_0$
- type II is false negative, we fail to reject $H_0$ but it was not true

**Multiple comparison corrections**
- if we make $n$ tests the errors stack, if we test 100 hypotheses at p-value=0.05, on average five of them will come out as positive on random
- so for multiple testing we usually correct for this, for example with bonferonni correction
$$\alpha_c=\frac{\alpha}{number\:of\:comparisons}$$
****

## Linear regression

## Nonlinear regression

## ANOVA

## Linear and nonlinear dimensionality reduction

## Clustering

## Robust statistics
