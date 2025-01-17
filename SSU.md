# SSU
> - Minimalizace empirického rizika a odhad chyby generalizace.
> - Odhad maximální věrohodnosti, EM algoritmus.
> - Standardní a hluboké neuronové sítě a jejich učení.
> - Modely se strukturovaným výstupem
> - Markovské modely, HMM.
> - Bayesovský odhad a skládání slabých klasifikátorů.

## Empirical risk minimization (ERM)

### ERM vocabulary
- **Predictor**
	- predicts a label $y$ from a feature $y=h(x)$
- **Loss function** 
  $$l(h(x),y) = l(\hat{y},y)$$
	- gives the penalty given a predicted label and the true label
- **True risk** 
  $$R(h) = \mathbb{E}[l(h(x),y)] = \sum_{x\in X}\sum_{y\in Y} p(x,y)\cdot l(h(x),y)$$
	- This is the true risk we are making, for it we need the joint pdf $p(x,y) = p(y) \cdot p(x|y) = p(x)\cdot p(y|x)$ , which we dont usually know
- **Empirical risk** 
  $$R(h) \approx R_{S^m}(h) = \frac{1}{m}\sum_{i = 1}^m l(h(x_i),y_i)$$
	- We estimate the true risk by an empirical risk, which is computed as the mean loss over all our examples
- **Bayes predictor** 
  $$R(h^*) = \inf_{h\in Y^X} R(h)$$
	- optimal predictior is $h^*$, that attains the minimal risk 
  $$h^*(x) = \arg\min_{y} R(h)$$
- Empirical risk can totally fail to be a good proxy for the true risk for some hypothesis classes

### Law of large numbers

### Hoeffding inequality

### Uniform law of large numbers

### VC dimension

## Maximum likelihood estimators
- ideal estimator should be
  - Unbiased:
    - mean of estimated parameters $\theta$ are the true parameters $\theta^*$
    $$\mathbb{E}_{\mathcal{T}^m \sim \theta^*}[e(\mathcal{T}^m)] = \theta^*$$

  - Have small variance:
    - estimates should not scatter to far from the mean estimate
  $$\mathbb{V}_{\mathcal{T}^m \sim \theta^*}[e(\mathcal{T}^m)]$$

  - Consistent:
    - the estimate gets better with more data points
  $$\mathbb{P}_{\theta^*}(|e(\mathcal{T}^m) - \theta^*| \geq \epsilon) \rightarrow 0 \text{ for } m \rightarrow \infty$$
    <!-- - Conditions for consistency [TODO but perhaps not needed] -->
- **Maximum likelihood estimator (MLE)**
  - not unbiased
  - however it is consistent and has the lowest possible variance
  - find parameters $\theta$ that maximise the likelihood (product of probabilities) that each point $x_i$ was drawn from the distribution $p_\theta$
  $$\hat{\theta} = \underset{\theta}{\arg\max}\: L(\theta) = \frac{1}{m}\prod_{i=1}^m p(x_i|\theta) = \frac{1}{m}\prod_{i=1}^m p_{\theta}(x)$$

### Parametric distributions
- distributions which we can describe with parameters
- exponential family is an important family that occurs frequently (for example gaussian, bernoulli, poisson all belong to it)
$$f(x|\theta) = h(x)\exp(\eta(\theta)^T T(x) - A(\theta))$$
  - where
    - $h(x)$ is the base measure (underlying measure)
    - $T(x)$ is the sufficient statistic
    - $\eta(\theta)$ is the natural parameter
    - $A(\theta)$ is the log-normalizer (or cumulant function)
- For proving that a distribution is from the exponential family we have to rewrite the distribution in the form given above, form the parameters and confirm their range
	- (1) The entire thing has to be in the form $e^{(thing)}$, so a common approach is to do $e^{\log(\text{stuff})}$
	- (2) $\eta$ - natural parameter, is a function of the distribution parameters $\theta$, needs to be multiplied by the random variable $x$
	- (3) $T$ - sufficient statistics, function of the random variable $x$
	 - (4) $A(\theta)$ - the rest, function of the parameters $\theta$

### [Kullback-Leibler divergence](https://www.countbayesie.com/blog/2017/5/9/kullback-leibler-divergence-explained)
- the entropy of a (discrete) distribution is 

  $$
  H(\mathcal{X}) = - \sum_{x \in \mathcal{X}}P(x)\cdot \log_k P(x)
  $$
 
  and represents the lower bound on the average number of bits (for $k = 2$) needed to represent a realization of the distribution (a sample)
- KL can be viewed as a slight modification of the entropy formula
  $$
  \text{KL}(\textcolor{#ffcccb}{P} \parallel \textcolor{#add8e6}{Q}) = \sum_{x \in \mathcal{X}} \textcolor{#ffcccb}{P(x)} (\log \textcolor{#ffcccb}{P(x)} - \log \textcolor{#add8e6}{Q(x)}) = 
  \sum_{x \in \mathcal{X}} \textcolor{#ffcccb}{P(x)} \log \frac{\textcolor{#ffcccb}{P(x)}}{\textcolor{#add8e6}{Q(x)}}\quad(0)
  $$

  where for each value that $\textcolor{#ffcccb}{P(x)}$ can take we look at how this differs wrt $\textcolor{#add8e6}{Q(x)}$ weighing the difference more where the density is higher for $\textcolor{#ffcccb}{P(x)}$ this has one important implication that KL divergence is *NOT* symmetric (and thus not a distance metric)

  $$\text{KL}(\textcolor{#ffcccb}{P} \parallel \textcolor{#add8e6}{Q}) \ne \text{KL}(\textcolor{#add8e6}{Q} \parallel \textcolor{#ffcccb}{P})$$
  we can also rewrite $(0)$ like this to express the expected amount of bits of information we lose when approximating $\textcolor{#ffcccb}{P}$ with $\textcolor{#add8e6}{Q}$
  $$
  \text{KL}(\textcolor{#ffcccb}{p} \parallel \textcolor{#add8e6}{q}) = 
  \mathbb{E}_{x \sim \textcolor{#ffcccb}{p}} \left[\log \frac{\textcolor{#ffcccb}{p(x)}}{\textcolor{#add8e6}{q(x)}}\right] =
  \mathbb{E}_{x \sim \textcolor{#ffcccb}{p}} \left[\log \textcolor{#ffcccb}{p(x)} - \log \textcolor{#add8e6}{q(x)}\right]
  $$
  or like this for continous probability distributions
  $$
  \text{KL}(\textcolor{#ffcccb}{p} \parallel \textcolor{#add8e6}{q}) =
  \int \textcolor{#ffcccb}{p(x)} (\log\textcolor{#ffcccb}{p(x)} -\log\textcolor{#add8e6}{q(x)}) dx =
  \int \textcolor{#ffcccb}{p(x)} \log \frac{\textcolor{#ffcccb}{p(x)}}{\textcolor{#add8e6}{q(x)}} dx
  $$

### EM algorithm
- We have data points for which we dont know the labels, we only know from which distributions they came from and how many distributions are there
- These distributions is then what we are trying to fit to the data, each point $x_i$ gets assigned probabilities that it belongs to each distribution $y_j,\: j\in \{1,\ldots,J\}$
- Auxillary variables $\alpha_x(y)$, s.t. $\sum_{y\in Y}\alpha_y(x) = 1$
	- This represents the posterior probability $\alpha_x(y) = p(y|x)$, e.g. the weight this datapoint has relative to each distribution $y$

- #### E-step
	- compute the posterior probabilities (e.g. the probability that point $x_i$ belongs to distribution $y_j$), can be computed by the bayes rule from the probability that distribution $y_j$ generated $x_i$ scaled by the probability that
  $$\alpha_{x_i}^{(t)}(y_j)=p_{\theta}(y_j|x_i)=\frac{p(x_i | y_j)p(y_j)}{\sum_k p(x_i | y_k)p(y_k)}$$
  - then for example for the gaussian distribution
  $$p(x_i|y_j)=\frac{1}{(2\pi)^{d/2}|\Sigma_j|^{1/2}}\exp\left(-\frac{1}{2}(x_i-\mu_j)^T\Sigma_j^{-1}(x_i-\mu_j)\right)$$
  - and $p(y_j)$ can either be set to $\frac{1}{J}$ (each distribution is expected to explain an equal proportion of the data) or it can be dynamically recomputed on the fly based on how many points have the highest probability for this distribution
- #### M-step
	- estimate the new parameters from the distribution assignments via maximum likelihood estimate, the contribution of each point to the estimate is weighted by the probability that it belongs to the $j$-th distribution
  $$\theta^{t+1}_j=\underset{x}{\arg\max}\:\mathbb{E}\left[ \sum_{y\in Y} \alpha_x^{(t)}(y_j)\:\log\:p_{\theta}(x,y_j)\right]$$
 - example M-step for gaussian distribution:
   Update parameters for each component $y_j$:

   Priors:
   $$p(y_j)^{(t+1)} = \frac{1}{N}\sum_{i=1}^N \alpha_{x_i}^{(t)}(y_j)$$

   Means:
   $$\mu_j^{(t+1)} = \frac{\sum_{i=1}^N \alpha_{x_i}^{(t)}(y_j)x_i}{\sum_{i=1}^N \alpha_{x_i}^{(t)}(y_j)}$$

   Covariance matrices:
   $$\Sigma_j^{(t+1)} = \frac{\sum_{i=1}^N \alpha_{x_i}^{(t)}(y_j)(x_i-\mu_j^{(t+1)})(x_i-\mu_j^{(t+1)})^T}{\sum_{i=1}^N \alpha_{x_i}^{(t)}(y_j)}$$

   where $N$ is the total number of data points.


## Markov Models
- Markov models are models that assume the following markov property:
  - *the future state depends only on the current state, not on the states before it*
  $$p(\bf{s}) = p(s_1) \cdot \prod_{i=2}^n p(s_i | s_{i-1})$$

### Markov Chain


### Hidden Markov Models

## Standard and deep neural networks and their learning

## Ensembling

- **Boosting**
  - sequentially train *low variance* *high bias* predictors
  - subsequent predictors learn to fix the mistakes of the previous ones
  - exploit dependence between learners
  - an example of this is Adaboost
- **Bagging (Bootstrap AGGregatING)**
  - sample different training sets from the original training set
  - train *high variance low bias* predictors on these sets and average their predictions
  - exploit independence between predictors
  - an example of this is a random forest

### Decision tree

### Random forest

### Adaboost

### Gradient Boosted Trees

### Gradient Boosting Machine
