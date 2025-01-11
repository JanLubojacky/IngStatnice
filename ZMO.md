# ZMO
> Segmentace dle textury, segmentace metodou aktivních kontur, křivky úrovně (level sets). modely tvaru a vzhledu. Nelineární registrace obrazů, registrace bodů, registrace na základě kritérií podobnosti, multimodální registrace. Detekce a lokalizace objektů v obrazu. Metody hlubokého učení pro segmentaci, detekci, klasifikaci a registaci.

## Fourier transform
- fourier transform
  $$X(\omega) = \int_{-\infty}^{\infty}x(t)\cdot e^{-j\omega t}\:dt$$
  $$X[k] = \sum_{n=0}^{N-1} x[n]\cdot e^{-j2\pi\frac{k}{N}n}$$
- inverse fourier transform
  $$x(t) = \frac{1}{2\pi}\int_{-\infty}^{\infty}X(\omega)\cdot e^{j\omega t}\:d\omega$$
  $$x[n] = \frac{1}{N} \sum_{k=0}^{N-1} X[k]\cdot e^{j2\pi\frac{k}{N}n}$$

## Wavelets
- small oscillations isolated in time
- a function $\psi(t)$ is a wavlet if
  - 1. it has zero mean (i.e. no DC component)
  $$\int_{-\infty}^{\infty}\psi(t)\:dt=0$$
  - 2. it has to have finite energy
  $$\int_{-\infty}^{\infty}\psi^2(t)\:dt<\infty$$
  <!-- - 3. it has to be orthogonal to itself -->
  <!-- $$\int_{-\infty}^{\infty}\psi(t)\psi^*(t)\:dt=0$$ -->

## Segmentation

**Active contours :snake:**
