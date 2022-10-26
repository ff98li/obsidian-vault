# Parallel Inversion of Neural Radiance Fields for Robust Pose Estimation
Yunzhi Lin .et al

## Goal
* Parallelised optimisation for estimating *6-DoF poses* with monocular RGB-only input.
* Approach:
	1. integrate *a momentum-based camera extrinsic optimisation* procedure into **Instant NGP**
	2. parallel Monte Carlo sampling to overcome local minima
	3. Robust pixel-based loss function
* Further explore the idea of pose estimation via **NeRF inversion** (iNeRF)

## Related works
#### Generalisable 6-DoF Pose Estimation

#### Instant NGP

#### iNeRF


## Methods

### Momentum-based Camera Extrinsics Optimisation

#### Camera Pose Representation

#### Momentum-Based Optimisation

* $\mathbf{r}_i = \mathbf{o} + t_i \mathbf{d}$
$$
\tau(\mathbf{d}) = \frac{1}{K}
	\sum^{K}_{i=1} t_i \mathbf{d} \times
	\frac{ \partial \mathcal{L} }{ \partial \mathbf{r}_i }
$$

### Parallelised Monte Carlo Sampling
* A single camera pose is vulnerable to local minima during optimisation iterations
*

### Pixel-based RGB Loss