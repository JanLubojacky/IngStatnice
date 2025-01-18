# MPV
> - Detekce bodů zájmu.
> - Hledání korespondencí mezi obrazy.
> - Detekce geometrických primitiv.
> - RANSAC.
> - Vyhledávání ve velkých obrazových databázích.
> - Sledování objektů.

## Correspondences
- the goal of finding Correspondences is to find the same semantic objects in two images and create a mapping between them, this is useful for tasks like 3D reconstruction, building 3D maps in robotics, tracking of objects, image stitching (panorama) and more
- the pipeline is the following:
  - detect important visual features (corners, blobs)
  - create descriptors of those features
  - match the descriptors
  - verification, outlier rejection
    - estimate homography
- also different neural nets can be used for the task such as [R2D2](https://arxiv.org/abs/1906.06195)

### Detectors

#### Harris (corner) detector
  - we want to find notable regions (patches), where the image function changes rapidly in both directions, for that we want to look for maxima in the following function
    $$E(\textcolor{#ffb3b3}{x_0},\textcolor{#b3d9ff}{y_0},\textcolor{#ffcccc}{u},\textcolor{#cce6ff}{v})=\sum_{(\textcolor{#ffb3b3}{x},\textcolor{#b3d9ff}{y})\in W(\textcolor{#ffb3b3}{x_0},\textcolor{#b3d9ff}{y_0})}w(\textcolor{#ffb3b3}{x},\textcolor{#b3d9ff}{y})\cdot (I(\textcolor{#ffb3b3}{x_0},\textcolor{#b3d9ff}{y_0}) - I(\textcolor{#ffb3b3}{x_0}+\textcolor{#ffcccc}{u},\textcolor{#b3d9ff}{y_0}+\textcolor{#cce6ff}{v}))^2$$
    - where $W(\textcolor{#ffb3b3}{x_0}, \textcolor{#b3d9ff}{y_0})$ is a window
    - $w(\textcolor{#ffb3b3}{x},\textcolor{#b3d9ff}{y})$ is a weight function (e.g. Gaussian)
    - the intuition is that for a flat region if we shift it the intensity will not change much,
    - for an edge it will change if we shift in one direction but not if we shift in another (parallel to the edge)
    - for a corner the difference between the patch and the shifted patch will be large if we shift it in any direction
  - the shift is approximated by a first order taylor expansion
    $$I(\textcolor{#ffb3b3}{x_0}+\textcolor{#ffcccc}{u},\textcolor{#b3d9ff}{y_0}+\textcolor{#cce6ff}{v}) \approx I(\textcolor{#ffb3b3}{x_0},\textcolor{#b3d9ff}{y_0}) + \textcolor{#ffb3b3}{dIx}\cdot \textcolor{#ffcccc}{u} + \textcolor{#b3d9ff}{dIy}\cdot \textcolor{#cce6ff}{v}$$
    which leads to approximating $E(\textcolor{#ffb3b3}{x_0},\textcolor{#b3d9ff}{y_0},\textcolor{#ffcccc}{u},\textcolor{#cce6ff}{v})$ as
    $$E(\textcolor{#ffcccc}{u},\textcolor{#cce6ff}{v}) = \begin{bmatrix} \textcolor{#ffcccc}{u} & \textcolor{#cce6ff}{v} \end{bmatrix} M \begin{bmatrix} \textcolor{#ffcccc}{u} \\ \textcolor{#cce6ff}{v} \end{bmatrix}$$
  - where
    $$M=\begin{bmatrix} \textcolor{#ffb3b3}{dIx}^2 & \textcolor{#ffb3b3}{dIx}\cdot\textcolor{#b3d9ff}{dIy} \\ \textcolor{#b3d9ff}{dIy}\cdot\textcolor{#ffb3b3}{dIx} & \textcolor{#b3d9ff}{dIy}^2 \\ \end{bmatrix} \in \R^{2\times2}$$
    is a hessian matrix for each pixel $(\textcolor{#ffb3b3}{x},\textcolor{#b3d9ff}{y})$
    - both eigenvalues of this matrix are large -> the gradient is changing in both $\textcolor{#ffb3b3}{x}$ and $\textcolor{#b3d9ff}{y}$ direction -> this is a good point (corner)
    - one or both small -> bad point (if only either $\textcolor{#ffb3b3}{x}$ or $\textcolor{#b3d9ff}{y}$ is changing this is an edge, if none this is a flat region)
  - for speed we approximate the eigenvalues with a response function
    $$R=det(M)-\alpha\cdot trace(M)^2$$
    - this works because:
      - $det(M)=\lambda_1\cdot\lambda_2$
      - $trace(M)=\lambda_1+\lambda_2$
      - so it will be large if both eigenvalues are large (corner), negative if one is large and the other small (edge), and approx zero if both are small (flat)
  - apply non-maximum suppression
  - **Properties**
    - Invariance to rotation (we lose very little points between different rotations in the image)
    - Invariance to translation
    - Not invariant to image scale (this is remediated with the usage of scale space)
#### SIFT
- used for detecting blobs, the idea is that blobs occur more often corners and are also a good feature for image matching
- [laplacian of gaussian is used to detect blobs](https://www.youtube.com/watch?v=uNP6ZwQ3r6A)
  - LoG can be aproximated by a difference of gaussians, this is combined with creating the scale space so a blob heatmap can be made by subtracting a less blurred image from a more blurred one, repeating this for different version of blur gives us blob detection across different scales
- so we can compute the gaussian scale-space first and then subtract the neighbouring scales to construct the difference of gaussians, this gives us the response
- finally we apply 3d nms and thresholding to filter the features
#### FAST
  - Select a pixel as the central pixel
  - Examine a circular neighborhood of (9 / 12 / 16) pixels around the central pixel.
  - Check if there is a sequence of consecutive pixels on the circle that are either brighter or darker than the central pixel by a set threshold value.
  - If such a contiguous set is found, the central pixel is considered a corner or an interest point.
    - the sequence should be $\approx\frac34$ of the circle so for example 12 pixels for the 16-neighbourhood
  - FAST has three levels of corner responses: 9, 12, and 16 pixels. The corner with the highest level response is selected.
  - FAST will report many maxima, so we apply a non-maxima supression after it.
#### Non-maxima suppression
- the detectors will usually create several responses per one feature, this redundance is undesired
- we slide a window across the image
  - if the pixel at the center of the image is maximal we retain it
  - if not we suppress it (lower its value, or set it to zero)
#### Scale space
- a pyramid of image at different resolutions
- different resolutions are obtained by blurring the image with a Gaussian filter with an increasing sigma
- we detect features for each scale and then use non-maxima suppression to only obtain features which occur at different places
- this allows us to detect features of different sizes
	- (this corresponds to the sigma at which the feature was found)
### Descriptors

#### Histogram of oriented gradients (HoG)
- a way to describe a patch via the magnitude of its gradients and their direction
- compute $dIx and $dIy$, gaussian derivative or gaussian blur + sobel filters can be used
- get angles and magnitudes for each pixel in the patch
  - $\phi=\arctan(\frac{dy}{dx})$
  - $m = \sqrt{(dx+dy)^2}$
    - weigh the magnitudes by a gaussian window

#### SIFT
- given a detected patch with important features we want to create a descriptor that can be used to represent it
- take a 16x16 window around the patch and scale it by a gaussian window
  - compute the dominant orientation of the keypoint by taking a 36-bin histogram of oriented gradients
  - take the peak in the histogram as the dominant orientation of the patch
- split the 16x16 window into 16 4x4 subregions
- for each sub-patch compute the histogram of oriented gradients (HoG) by
    - quantizing the angles to 8 directions ($45\degree$ per bin)
    - the angles are measured relative to the dominant orientation of the keypoint to provide rotation invariance so we take $\theta_{\text{this}} - \theta_{\text{dominant}}$ then if everything gets rotated by some degree the subtraction will still be the same angle
- concat all sub-patch histograms into a single vector, that will be $16$ sub-patches $\cdot$ $8$ angles per sub-patch $\rightarrow$ $128$-dim descriptor for the entire patch

### Matching
- **nearest neighbor**
  - just match based on l2 distance
  - allows many to one (asymmetric) matches, so it is not very reliable
- **mutual nearest neighbor**
  - only retain symmetric matches (e.g. matches, where both points are mutual nearest neighbours)
- **second nearest neighbour ratio**
  - in addition to nearest neighbor take also the second nearest and make sure the ratio is lower, than some threshold (0.8)
  - in this way we can discard the mapping in case there are any features close by that could be mapped to the same point and thus only keep confident matches
  - we can also combine this with the mutual nearest neighbor for more robustness
### Spatial Verification (w RANSAC)

#### Homography
  - we have a feature in two images, the idea is that under the assumptions that the only changes are in viewpoint, scale and rotation we can map the features on each other using linear transformations 
  $$\lambda\begin{bmatrix}x'\\y'\\1\end{bmatrix}=\begin{bmatrix}h_{11}&h_{12}&h_{13}\\h_{21}&h_{22}&h_{23}\\h_{31}&h_{32}&h_{33}\end{bmatrix}\begin{bmatrix}x\\y\\1\end{bmatrix}$$
  - for each two points the following equations then hold
  $$\begin{align}x'(h_{31}x+h_{32}y+h_{33})-(h_{11}x+h_{12}y+h_{13})=0\\y'(h_{31}x+h_{32}y+h_{33})-(h_{11}x+h_{12}y+h_{13})=0\end{align}$$
  - this system is underdetermined, we need at least 4 pairs of points to obtain unique solution
  - putting the system of equations into a matrix form for $n$ points in general gives $Ah=0$, where
  $$\begin{bmatrix}
  -x_1 & -y_1 & -1 & 0 & 0 & 0 & x'_1x_1 & x'_1y_1 & x'_1 \\
  0 & 0 & 0 & -x_1 & -y_1 & -1 & y'_1x_1 & y'_1y_1 & y'_1 \\
  -x_2 & -y_2 & -1 & 0 & 0 & 0 & x'_2x_2 & x'_2y_2 & x'_2 \\
  0 & 0 & 0 & -x_2 & -y_2 & -1 & y'_2x_2 & y'_2y_2 & y'_2 \\
  \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots \\
  -x_n & -y_n & -1 & 0 & 0 & 0 & x'_nx_n & x'_ny_n & x'_n \\
  0 & 0 & 0 & -x_n & -y_n & -1 & y'_nx_n & y'_ny_n & y'_n
  \end{bmatrix}
  \begin{bmatrix}
  h_{11} \\
  h_{12} \\
  h_{13} \\
  h_{21} \\
  h_{22} \\
  h_{23} \\
  h_{31} \\
  h_{32} \\
  h_{33}
  \end{bmatrix} = 0
  $$
  - $Ah=0$ will have a unique solution for $4$ points, though for this to work the $A$ must have a full rank, this means that out of the four, three points can't lie on a line
	- if we use more than four pairs, this is an overdetermined system of equations, we want to solve this $Ah=0$ and we do not care about the trivial solution $h=0$ so we require $||h||^2=1$
  - we formulate the task like a constrained least squares problem
  $$\min ||Ah||^2\quad \text{s.t.}\:||h||^2=1$$
  - solving with lagrange mutlipliers we obtain a solution
  $$(A^TA-\lambda I) h=0$$
  - which corresponds to the eigenvector for the smallest (since we want to minimize the expression) eigenvalue of $A^T A$, these can be obtained with SVD decomposition of matrix $A$

#### RANSAC (random sample concensus)
- **When to use RANSAC**
  - we noisy data with a lot of outliers so fitting our model on all of them wont produce good results, but we still want to fit a model that a good part of the data will follow

- **Algorithm**
  - for each iteration
    - randomly select minimum samples needed to estimate the model
    - estimate the model
    - select inliers (point probable under the model, e.g. points within some threshold from the model) and outliers (points not probable under the model)
    - evaluate the model fit, usually with the inlier ratio $\frac{\text{inliers}}{\text{all points}}$
    - if we get a new highest number of inliers keep this model as the best model
- **Parameters**
  - number of iterations
  - threshold for inlier points
- **Stopping criterion**
  - probability $\text{conf}$, that we will get a better solution, than what we already got 
  - $\text{new max iter}=\frac{\log(1 - \text{conf})}{\log(1 - \text{inl ratio}^\text{sample size})}$

## Retrieval
### BoW
- **BoW construction**
  - place all feature descriptors into one space
  - cluster with k-means, number of clusters depends on what we want to do with it
  - cluster centres form visual words
  - then inserting all the images in the database looks like this:
    - describe each image by its features,
    - assign each feature to the closest cluster (visual word)
    - create a histogram of the visual words present in the image, this can then be used as a vector that describes the image
- **Query**
  - compute visual words associated with the query image, create a description vector for the image in the same manner as above
  - compute cosine similarity between the query vector and the database matrix, with this we can quickly compare the query to every sample in the database
- **Inverted file structure**
  - memory efficient representation, instead of having each image as key and visual words it contains as values we have a visual words as a key and images it appears in as values
- **Inverse document frequency** 
  $$idf_i = \log \frac{\text{\#images} }{\text{\#images with word i} }$$
  - visual words which appear less have bigger weights as they give us more information than words which appear often
- Spatial veryfication (w RANSAC)
  - matching the query against every image in database would be expensive, but running it on top 10-20 hits is a good idea to rank them better
### VLAD (vector of locally aggregated descriptors)
- builds on top of BoW
- builds the clusters same as BoW
- then for each image we aggregate (sum up) the residuals between the cluster center (visual word) and all the image features from our image that are assigned to it, this is repeated for all the visual words so we end up with one aggregated descriptor vector per visual word, these are then concateneted into a final description vector that is used for the image as a database entry
- these vectors are then used instead of the visual words to describe an image

## Deep Metric Learning

## Tracking
- Tracking can be achieved with Correspondences or with Segmentation
- **Optical flow**
	- patterns of motion for objects between successive frames
	- compute the apparent "movement" of an object in an image

### KLT tracker
- we have a region of interest $x$ and a template image $T$
- and we want to estimate a transformation of a position 
  $$W(x,p):\mathbb{R}^2\rightarrow \mathbb{R}^2$$
- from the template image $T$ to a transformed image $I$ such that 
  $T(x)$~$I(W(x,p^*))$ for some $p^*$, which can be a homology such as translation, rotation, shift, …
  - we assume brightness constancy: the intensity of a point remains constant as it moves between frames
    $$I(x,y,t) = I(x+dx,y+dy,t+dt)$$
- we can define the task as: 
  $$\begin{align} SSD(p)=\sum_{x\in\text{ROI}}[I(W(x;p))-T(x)]^2 \end{align}$$
  - find a $p^*$ such that 
    $$p^*=\arg\min_{p} SSD(p)$$
    - we solve it by:
    1. linearizing the problem wrt. $p$ using first order taylor approximation (derivatives in the x & y direction), this approximation works exactly only if the function is linear, which it most likely isn't, but it will still get us closer
    2. now we can solve it as a least squares problem
    3. first step is an approximation, so iterate to get closer again
    4. The algorithm runs for a limited number of steps if it doesn't converge for a point in time drop the point
- **KLT updates**
  - for a point $(k,l)$ in some patch / window $W$, if we linearize $(3)$ and assume only translation we obtain the optical flow equation
    $$dIx(k,l)u+dIy(k,l)v=-dIt(k,l)$$
    where $dIx,dIy$ are spatial derivatives and $dIt$ is the time derivative
  - for an image patch with $n$ points we can put the spatial derivatives into matrix $A\in \mathbb{R}^{2\times n}$ and time derivative into matrix $B\in\mathbb{R}^{n}$ and write it like this
    $$A\mathbf{u}=B$$
    where
    $$
    \begin{bmatrix} 
    I_x(p_1) & I_y(p_1) \\
    \vdots & \vdots \\
    I_x(p_n) & I_y(p_n)
    \end{bmatrix}
    \begin{bmatrix}
    u \\
    v
    \end{bmatrix} = 
    \begin{bmatrix}
    -I_t(p_1) \\
    \vdots \\
    -I_t(p_n)
    \end{bmatrix}
    $$
    - $\mathbf{u}$ is the velocity vector
    - $B$ is computed as $B=T(p)-I(p)$, where $T(p)$ is the flattened template patch and $I(p)$ is the flattened patch from the next frame
  - we can find the least squares solution for this as $\mathbf{u}=(A^TA)^{-1}A^TB$
  - then we update the position of the tracked patch with this solution
  - this will of course fail where $(A^TA)$ is singular which corresponds to patches where harris would fail (flat regions / edges)
- **Properties of the KLT tracker**
  - it will work well, where Harris detector works well
  - so ideal points for tracking are corners
  - it cannot re-detect objects
- **Summary**
  - initialize features with harris detector
  - estimate the optical flow between consecutive frames and move the tracked points accordingly

### Discriminative tracking
- pose tracking as a detection problem
- we pose the tracking as a linear classifier with a sliding window, this corresponds to correlation
- **Correleation filers**
  - in the first frame define the template $w$
  - then in each subsequent frame compute sliding window correlation
    $$y=\mathbf{w}^T\mathbf{x}$$
  - cross correlation (convolution) can be done very fast in the fourier domain as one dot product for the entire image (element wise product)
    $$\mathbf{\hat y}=\mathbf{\hat x^*} \times \mathbf{\hat w}$$
    where $x^*$ is complex conjugate (invert sign for the complex part)
  - then we can compute the maxima from the activation map
- **Problems & solutions**
  - pure correlation has a huge response, so the localization is not very precise
  - we want sharp peak $\rightarrow$ good localization
  - what can be used is a gaussian detection filter with a mix of low and high frequencies
- **MOSSE filter (min output sum of sq. errors)**
  - another filter to provide better localization
  - we want to find a filter with the maximum correlation for the template object 
  - objective: 
    $$\min_w||x*w-g||^2$$
  - solution:
    $$\hat w_{new}=\frac{\hat g \times \hat x}{\hat x^* \times \hat x + \lambda}$$
  - update the old template with some percentage (e.g. $\eta = 0.01$) of the new template, this improves rotational invariance, background / translational invariance 
  $$\hat w_t=(1-\eta)\hat w_{t-1}\cdot \eta \:\hat w_{new}$$
  - **scale estimation**
    - have a scale of 0.9, 1 and 1.1, which corresponds to differently sized bounding boxes
    - and estimate the changing scale in every frame, in this way the maximum change in one second at 25 fps is $1.1^{25}\approx10.8$ and $0.9^{25}\approx 0.07$ 
- **kernelized correlation filters**
  - instead of a linear correlation filter we can employ the kernel trick
  - we can formulate the tracking as a regularized least squares problem
    $$\min_w\left(||Xw−y||^2+λ||w||^2\right)$$
    where $X$ is a circulant matrix
  - solution: 
    $$w=(X^TX+λI)^{−1}X^Ty$$
  - which can be (for a circulant matrix) computed as 
    $$\hat w_{new}=\frac{\hat x \times \hat y}{\hat x^* \times \hat y + \lambda}$$
    - where hats denote fourier image, and $\times$ denotes component-wise multiplication
  - The same thing can be expressed with a kernel, e.g. map the $x$ with some non-linear function $\varphi(x)$
  - The solution changes to 
    $$\mathbf{\hat\alpha}=\frac{\mathbf{\hat y}}{\mathbf{\hat k}^{xx}+\lambda}$$
- **Improvements**
  - the correlation / kernelized correlation can be used on deep-learned features
- ## Optical flow estimation
- track each point and its change
- **Deep nets**
  - namely U-NET (encoder+decoder with skip connections)
  - it has the advantage that it learns, we do not have to define discrepancy and smoothing functions for estimating the optical flow

### [Hough transform](https://www.youtube.com/watch?v=XRBc_xkZREg)
- used to detect simple geometric shapes such as lines or circles
- it works well even if the shape is only outlined by separated points
- example for detecting lines:
  - in image space (axis $x$ and $y$) we have lines represented by this equation
  $$y_i=ax_i+b$$
  - the parameter space (axis $b$ and $a$) is formed by
    $$b=-ax_i+y_i$$
  - a point in image space then corresponds to a line in parameter space (the parameters a,b on that line represent all the possible lines that pass trough that point in image space)
  - we create an accumulator array $A(a,b)$ that contains quantized values (the range of the cells is an important hyperparameter) for parameters $a$ and $b$
  - for each detected point in image space we record the line in parameter space
  - the lines in parameter space will intersect if the points lie on a line in the image space and the parameters (a,b) at the intersection will be the parameters of the line that connects them
  - then we can take local maxima in $A$ to get parameters $a,b$ that correspond to lines present in the image, the value in the accumulator array for $a,b$ also indicates how many points lie on a line defined by them
  - **better parametrization**
    - slope of the line $a \in \R$ which would make the accumulator array very large for practical purposes
    - we parametrize the line with
      $$x\sin\theta-y\cos\theta+\rho=0$$
      where $0\le\theta\le\pi$ is the angle of the line with the $x$ axis and $\rho$ is the distance of the line from the origin, which can't be larger than the size of the image
    - this will make the lines appear as sinusoids in the parameter space however the algorithm with accumulating sinusoids for all points and then finding the local maxima stays the same
