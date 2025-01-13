# ZMO
> Segmentace dle textury, segmentace metodou aktivních kontur, křivky úrovně (level sets). modely tvaru a vzhledu. Nelineární registrace obrazů, registrace bodů, registrace na základě kritérií podobnosti, multimodální registrace. Detekce a lokalizace objektů v obrazu. Metody hlubokého učení pro segmentaci, detekci, klasifikaci a registaci.

## Fourier transform
- fourier transform
  $$X(\omega) = \int_{-\infty}^{\infty}x(t)\cdot e^{-j\omega t}\:dt$$
  $$X[k] = \sum_{n=0}^{N-1} x[n]\cdot e^{-j2\pi\frac{k}{N}n}$$
- inverse fourier transform
  $$x(t) = \frac{1}{2\pi}\int_{-\infty}^{\infty}X(\omega)\cdot e^{j\omega t}\:d\omega$$
  $$x[n] = \frac{1}{N} \sum_{k=0}^{N-1} X[k]\cdot e^{j2\pi\frac{k}{N}n}$$

## [Wavelets](https://www.ee.columbia.edu/~aurel/public/guide99.pdf)
- a wavelet is a small oscillations isolated in time
- With a Fourier transform we can retrieve information about the frequencies in the signal but the time resolution is encoded in the phase of the complex numbers so it is not easy to decode where in the signal the frequencies occur.
- We convolve the signal with small oscillations (wavelets) and tune both their frequency and position in time. Via this we obtain a joint view of both the frequencies in the signal and the time where they occur.
- a continuous wavelet transform (CWT) is defined as
  $$\gamma(s,\tau) = \int f(t)\cdot \psi^*_{s,\tau}(t)\:dt$$
    where $\psi_{s,\tau}(t)$ is the wavelet and $f(t)$ is the signal, $\psi_{s,\tau}(t)$ are generated from a mother wavelet
  $$\psi_{s,\tau}(t) = \frac{1}{\sqrt{s}}\psi\left(\frac{t-\tau}{s}\right)$$

  by moving them in time $t$ and scaling them by a factor $s$

- a function $\psi(t)$ is a wavelet if
  - 1. it has zero mean (i.e. no DC component)
  $$\int_{-\infty}^{\infty}\psi(t)\:dt=0$$
  - 2. it has to have finite energy
  $$\int_{-\infty}^{\infty}\psi^2(t)\:dt<\infty$$
  - 3. it satisfies the admissibility constraint
  $$\int_{-\infty}^{\infty} \frac{|\Psi(\omega)|^2}{|\omega|} d\omega < \infty$$

- Wavelets are complex functions, the 1D plots of them are usually just the real part

## Segmentation

### Active contours :snake:

<!--**Gradient vector flow (GVF)**-->

### Level sets

### Active shape models (ASM)

## Registration
- **rigid registration** : only rotations, translations, scaling
- **non-rigid registration** : allows local deformations and warping

### Iterative closest point registration (ICP)
- an algorithm used for aligning point clouds (image stitching) with rigid registration
- given two sets of point clouds  $P_t$ and $P_{t+1}$
- perform the following steps until the algorithm converges or maximum steps are reached

**1. Correspondence Matching**
  - Find correspondence clusters $c_i=\{(x_j,y_k),…\}$ by assigning each point $y_j \in P_{t+1}$ to the closest point $x_k \in P_t$ from the other cluster
  - Closest points can be found quickly via k-D trees, with that we gain a speedup of $O(N_yN_x)\rightarrow O(N_y\log N_x)$

**2. Translation Alignment**
- Translate point cloud $P_{t+1}$ to align centroids of corresponding clusters
- For each cluster $c_i$, ensure:
$$
\bar{x}_i = \bar{y}_i \quad \forall i
$$

**3. Rotation Optimization**
- 1. Find optimal rotation matrix $\mathbf{R}$ by solving the least squares problem:
     $$
     \underset{\mathbf{R}^{\top} \mathbf{R}=\mathbf{I},|\mathbf{R}|=1}{\arg \min } \sum_i \left\| \mathbf{y}_i - \mathbf{R} \mathbf{x}_i \right\|^2
     $$

- 2. Expand the squared norm:
     $$
     \sum_i \left\| \mathbf{y}_i - \mathbf{R} \mathbf{x}_i \right\|^2 = \sum_i (\mathbf{y}_i - \mathbf{R} \mathbf{x}_i)^{\top}(\mathbf{y}_i - \mathbf{R} \mathbf{x}_i)
     $$
     $$
     = \sum_i (\mathbf{y}_i^{\top}\mathbf{y}_i + \mathbf{x}_i^{\top}\mathbf{R}^{\top}\mathbf{R}\mathbf{x}_i - 2\mathbf{y}_i^{\top}\mathbf{R}\mathbf{x}_i)
     $$

- 3. Since $\mathbf{R}$ is a rotation matrix, $\mathbf{R}^{\top}\mathbf{R}=\mathbf{I}$, so:
     $$
     = \sum_i (\mathbf{y}_i^{\top}\mathbf{y}_i + \mathbf{x}_i^{\top}\mathbf{x}_i - 2\mathbf{y}_i^{\top}\mathbf{R}\mathbf{x}_i)
     $$

- 4. The first two terms are constant with respect to $\mathbf{R}$, so minimizing is equivalent to maximizing:
     $$
     \underset{\mathbf{R}^{\top} \mathbf{R}=\mathbf{I},|\mathbf{R}|=1}{\arg \max } \sum_i \mathbf{y}_i^{\top}\mathbf{R}\mathbf{x}_i
     $$

- 5. Using the trace property $a^{\top}b = trace(ba^{\top})$ for vectors:
     $$
     \underset{\mathbf{R}^{\top} \mathbf{R}=\mathbf{I},|\mathbf{R}|=1}{\arg \max } \sum_i trace(\mathbf{R}\mathbf{x}_i\mathbf{y}_i^{\top})
     $$

- 6. By linearity of trace:
     $$
     \underset{\mathbf{R}^{\top} \mathbf{R}=\mathbf{I},|\mathbf{R}|=1}{\arg \max } trace(\mathbf{R}\sum_i \mathbf{x}_i\mathbf{y}_i^{\top})
     $$

- 7. Define cross-covariance matrix $\mathbf{H} = \sum_i \mathbf{x}_i\mathbf{y}_i^{\top}$, giving final form:
     $$
     \underset{\mathbf{R}^{\top} \mathbf{R}=\mathbf{I},|\mathbf{R}|=1}{\arg \max } trace(\mathbf{R}\mathbf{H})
     $$
- 8. This problem is solved using SVD of $\mathbf{H} = \mathbf{U}\mathbf{\Sigma}\mathbf{V}^{\top}$. The optimal rotation is:
     $$
     \mathbf{R} = \mathbf{V}\begin{pmatrix} 
     1 & 0 & 0 \\
     0 & 1 & 0 \\
     0 & 0 & \det(\mathbf{V}\mathbf{U}^{\top})
     \end{pmatrix}\mathbf{U}^{\top}
     $$
     where the middle matrix ensures $|\mathbf{R}|=1$ (proper rotation, not reflection).
- 9. The SVD solution is optimal because:
     $$\mathbf{H} = \mathbf{U}\mathbf{\Sigma}\mathbf{V}^{\top}$$
      by cyclic property of the trace
     $$trace(\mathbf{R}\mathbf{H}) = trace(\mathbf{R}\mathbf{U}\mathbf{\Sigma}\mathbf{V}^{\top}) = trace(\mathbf{V}^{\top}\mathbf{R}\mathbf{U}\mathbf{\Sigma})$$

     Define
     $$\mathbf{M} = \mathbf{V}^{\top}\mathbf{R}\mathbf{U}$$

     Since $\mathbf{R}$ is orthogonal, $\mathbf{M}$ must also be orthogonal:
     $$\mathbf{M}^{\top}\mathbf{M} = \mathbf{U}^{\top}\mathbf{R}^{\top}\mathbf{V}\mathbf{V}^{\top}\mathbf{R}\mathbf{U} = \mathbf{I}$$
     
     For any orthogonal $\mathbf{M}$ and diagonal $\mathbf{\Sigma}$ with non-negative entries:

      $$trace(\mathbf{M}\mathbf{\Sigma}) \leq trace(\mathbf{\Sigma})$$

     with equality if and only if $\mathbf{M}=\mathbf{I}$
     
     Therefore maximum is achieved when:
     $$\mathbf{V}^{\top}\mathbf{R}\mathbf{U} = \mathbf{I}$$
     
     Solving for $\mathbf{R}$:
     $\mathbf{R} = \mathbf{V}\mathbf{U}^{\top}$
     
     The middle matrix in step 8 ensures $|\mathbf{R}|=1$ for a proper rotation.
<!-- - 1. Find optimal rotation matrix $\mathbf{R}$ by solving the least squares problem: -->
<!--       $$ -->
<!--       \underset{\mathbf{R}^{\top} \mathbf{R}=\mathbf{I},|\mathbf{R}|=1}{\arg \min } \sum_i \left\| \mathbf{y}_i - \mathbf{R} \mathbf{x}_i \right\|^2 -->
<!--       $$ -->

<!-- - 2. Compute point covariance matrix: -->
<!--       $$ -->
<!--       \mathbf{M}=\mathbf{X}^{\top}\mathbf{Y} -->
<!--       $$ -->
<!--       where $\mathbf{X}, \mathbf{Y} \in \mathbb{R}^{3 \times n}$ (for 3D) -->
<!---->
<!-- - 3. Transform the optimization problem to: -->
<!-- $$ -->
<!-- \underset{\mathbf{R}^{\top} \mathbf{R}=\mathbf{I},|\mathbf{R}|=1}{\arg \max } \operatorname{tr}(\mathbf{M R}) -->
<!-- $$ -->
<!---->
<!-- - 4. Solve using Singular Value Decomposition (SVD): -->
<!--   - Decompose $\mathbf{M}=\mathbf{U}\mathbf{\Sigma}\mathbf{V}^T$ -->
<!--   - Compute rotation matrix as $\mathbf{R}=\mathbf{V}\mathbf{U}^{\top}$ -->
### Coherent point drift

