# SSU
> - Minimalizace empirického rizika a odhad chyby generalizace. 
> - Odhad maximální věrohodnosti, EM algoritmus.
> - Standardní a hluboké neuronové sítě a jejich učení.
> - Modely se strukturovaným výstupem
> - Markovské modely, HMM.
> - Bayesovský odhad a skládání slabých klasifikátorů.

## Empirical risk minimization
- **Predictor**
	- predicts a label $l$ from a feature $$h(x) = y$$
- **Loss function** $$l(h(x),y) = l(\hat{y},y)$$
	- gives the penalty given a predicted label and the true label
- **True risk** $$R(h) = \mathbb{E}[l(h(x),y)] = \sum_{x\in X}\sum_{y\in Y} p(x,y)\cdot l(h(x),y)$$
	- This is the true risk we are making, for it we need the joint pdf $p(x,y) = p(y) \cdot p(x|y) = p(x)\cdot p(y|x)$ , which we dont usually know
- **Empirical risk** $$R(h) \approx R_{S^m}(h) = \frac{1}{m}\sum_{i = 1}^m l(h(x_i),y_i)$$
	- We estimate the true risk by an empirical risk, which is computed as the mean loss over all our examples
- **Bayes predictor** $$R(h^*) = \inf_{h\in Y^X} R(h)$$
	- optimal predictior is $h^*$, that attains the minimal risk $$h^*(x) = \arg\min_{y} R(h)$$
- Empirical risk can totally fail for certain fail to be a good proxy for the true risk for some hypothesis classes

### Law of large numbers

### Hoeffding inequality

### Uniform law of large numbers

### VC dimension

## Markov Models

### Hidden Markov Models

## Standard and deep neural networks and their learning

### Bayes estimate and ensemble methods
