# SAN
> - lineární regrese (předpoklady, odhad parametrů modelu, interpretace modelu, výběr relevantních proměnných, statistické srovnání mezi modely)
> - nelineární regrese (příklady konkrétních algoritmů a jejich srovnání), 
> - analýza rozptylu
> - lineární a nelineární metody redukce dimenze (cíle, příklady konkrétních algoritmů, jejich vlastnosti a srovnání)
> - shlukování (definice úlohy a její složitost, příklady hierarchických a nehierarchických metod shlukování, spektrální shlukování)
> - robustní statistika (motivace pro robustní statistiku a příklady metod)

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

**Family-wise error rate (FWER)**
- when testing multiple hypotheses, the FWER is the probability of at least one false positive happening, this is calculated as 1 minus the probability of none of the errors happening which is much easier to compute

$$FWER(\alpha) = 1-(1-\alpha)^{n}$$

- an example procedure for correcting FWER is the Bonferroni correction
$$\alpha_c=\frac{\alpha}{number\:of\:comparisons}$$

**False discovery rate (FDR)**
- for large number of tests trying to not make any mistake (e.g. limiting the $FWER$) would be too strict and we would make a lot of type II. errors instead
- so we might try to limit the ratio of false positives (false discoveries) to the total number of positives (all discoveries), this is what the false discovery rate does

$$FDR = \frac{FP}{FP+TP}$$

we minimize the expected value of this, an example method for correcting FDR is the Benjamini-Hochberg procedure

**Benjamini-Hochberg procedure**

Given $m$ tests:

1. For a given  $\alpha$, find the largest $k$ such that

$$P_{(k)} \le \frac{k}{m}\alpha$$

2. Reject the null hypothesis (i.e., declare discoveries) for all

$$H_{(i)}\quad i=1,\ldots ,k$$

## Linear regression

- given independent variables (data) $\mathbf{X}$ and a dependent variable (measurements) $y$ we want to fit a linear model that predicts $y$ given $\mathbf{X}$
- **Assumptions**
  - the data follows a linear model
  $$Y=\beta_0 + \beta \cdot X + \epsilon$$
  - the residuals are i.i.d. (independent and identically distributed) following $\epsilon \sim \mathcal{N}(0,\sigma^2)$ (homogeneous variance, homoscedasticity)
  - no multicollinearity: the independent variables are not correlated
  - dependent variable is not autocorrelated
  - under these assumtions we can use the following model to model the data

  $$y=\beta_0+\sum_{i=1}^n \beta_i\cdot x_i$$

- **Definition**
  - formally we can define linear regression as a minization problem

  $$\min_\beta\|X\cdot\beta-y\|^2_2 = \min_\beta(X\cdot\beta-y)^T(X\cdot\beta-y)$$

- **R Squared**
  - used to measure how well the model fits the data
  - $TSS$ (total sum of squares), the null model, calculated as the mean of the data
  - $RSS$ (residual sum of squares), residuals for the model fit to the data
  $$R^2=1-\frac{RSS}{TSS}$$
- **Adjusted R Squared**
  - $R^2$ has one problem, it will always increase with more variables added to the model, adjusted $R^2$ accounts for the loss in degrees of freedom
  $$R^2_{adj.} = 1-\frac{RSS/(m-p-1)}{TSS/(m-1)}$$
  - where $m$ is the number of observations and $p$ is the number of independent variables
  - this allows to compare models with different number of independent variables and shows overfitting if the

- **Regularization**
  - **Stepwise selection**
    - both are greedy algorithms that either add or remove variables based on the performance of the model
    - both have a complexity of $O(N^2)$, which gives the entire process a complexity of $O(N^2 \cdot O(\text{model fitting}))$ so it is quite expensive
      - ($N + (N-1) + ... + 2 + 1 = N(N+1)/2$)
    - **Forward Stepwise Selection**
      - given $N$ independent variables fit $N$ models, each with one feature $y=f(x_i),\:i\in\{1,\ldots,N\}$
      - then select the best one out of those $y=f(x_b)$ and fit $N-1$ models $y=f(x_{b},x_{i}),\:i\in\{1,\ldots,N\}$
      - continue adding variables to the model based on which improve the performance the most, afterwards use the model with the best resulting performance
    - **Backward Stepwise Selection**
      - starts with full model and removes variables one by one based on which removal increases the performance of the model the most
      - use the model with the overall best performance
  - **Shrinkage**
    - we add penalty terms to the objective function, this can be though of as giving the model a "budget" for the weights, so it is forced to give high weights only to the coefficients which improve the objective the most
    - **Lasso**
      - $L_1$-norm (sum of absolute values)
      - This will push coefficients to zero
      - No closed form solution unlike least squares or ridge (because of the $L_1$-norm being non-differentiable at 0), can be solved with gradient descent based methods (such as coordinate descent)
      $$\min_\beta\|A\cdot\beta-y\|^2_2+\lambda\|\beta\|_1$$
    - **Ridge**
      - $L_2$-norm (sum of squared values)
      - This will push coefficients to small values but not to zero
      $$\min_\beta\|A\cdot\beta-y\|^2_2+\lambda\|\beta\|_2^2$$
    - **Elastic Net**
      - a mix of both lasso and ridge
      $$\min_\beta\|A\cdot\beta-y\|^2_2+\lambda_1\|\beta\|_1 + \lambda_2\|\beta\|_2^2$$

- **Generalized linear models (GLM)**
  - ...

- **AIC (Akaike Information Criterion)**
	- $\text{AIC} = 2\cdot k-2\ln(\hat L)$
	- where
      $$\hat L = \mathcal{L}(\theta|x) = P(y|\theta) = \prod^n_i P(y_i|\theta)$$
      is a likelihood function that expresses how likely is it to see the data given the model parameters and $k$ is the number of parameters
	- can be helpful in cases, when we can not easily evaluate the models on a test set, which is more commonly used in practice as it has less assumptions and can be used to also compare models with different number of features for example
	- the lower the AIC is the better (this can be easily seen from the equation

## Nonlinear regression
- **Polynomial regression**
- **Piece-wise constant**
- **Local regression**
- **Spline regression**

## ANOVA

## Linear and nonlinear dimensionality reduction

**Definition of the task**

**Principal component analysis (PCA)**

**Kernel PCA**

**Isomap**

**Locally linear embedding**

**t-SNE**

**UMAP**

## Clustering
**Formal definition**
- split unclassified objects (usually from $\mathbb{R}^n$) into $k > 1$ mutually disjoint sets (clusters)
- partition $\mathcal{X}$ into $\Omega = \{C_1, \ldots, C_k\}$ such that 
$$\forall i,j \leq k, i \neq j: C_i \neq \emptyset,\:C_i \cap C_j = \emptyset,\:C_1 \cup C_2 \cup \cdots \cup C_k = \mathcal{X}$$
- search space size is Stirling number of the second kind
  $$S(m,k)=\begin{Bmatrix} n \\ k \end{Bmatrix} = \frac1{k!}\sum_{j=0}^k (-1)^{(k-j)} \binom{k}{j}j^m$$
  which grows very quickly and as such the problem is NP-hard, different heuristic algorithms exist

**K-means clustering**

**Hierarchical clustering**

**Density based clustering**

**Spectral clustering**

## Robust statistics

