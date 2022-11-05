## Homogeneous Reconstruction
* Jointly learn the density volume $V$ and the pose of each image $\phi_i \in SO(3) \times \mathbb{R}^2$

### Iterative Refinement
An MAP estimate of $V$ marginalising over the posterior for $\phi_i$ via EM:
$$
\DeclareMathOperator*{\argmax}{arg\,max}
	V^{MAP} = \argmax_V \sum^N_{i = 1}
	\log \int p( X | \phi, V ) p(\phi)d\phi + \log p(V)
$$
* E-step:
	* Given $V^{(n)}$ the estimate of the volume at iteration $n$, images are aligned with $V^{(n)}$
* M-step:
	* The images are back-projected to yield $V^{n+1}$ with the updated alignments

This iterative refinement is a local optimisation procedure whose result depends on the initial model $V^{(0)}$.

In practice, reconstruction typically proceeds in 2 stages:
1. Generation of a low-resolution initial model $V^{(0)}$
2. Iterative refinement of $V^{(0)}$ to high resolution

* Generation of $V^{(0)}$ via
	* experiment
	* *ab initio* reconstruction with SGD on $V^{MAP}$
		* fails to converge to the correct local minima for datasets with significant structural heterogeneity

### Resolution Estimation and Validation
* 3-D reconstruction tackles an inverse problem with no ground truth
	* Challenge: validation of the final $V^{(n)}$
* Issue:
	* Bias from $V^{(0)}$ (<u>Reference bias</u>)
	* Artifacts in $V$ due to the high noise level in the images and local convergence properties of iterative refinement (highly non-convex objective)
* Current standard:
	* <u>2-fold cross-validation</u> based on the **Fourier Shell Correlation** curve
		* Split the image set of the single particle into 2 independent equal sets randomly, and generate independent 3-D reconstruction from each set respectively
		* *How well does one independent reconstruction match the other in terms of spatial resolution?*
			* Calculate the 3-D Fourier transform for the 2 reconstructions
			* Use FSC to measure similarity between the two independent reconstruction from low to high spatial frequency i.e. shell by shell from the origin outwards

The FSC curve meaasures correlation between volumes as a function of **spherically-averaged radial shells** in Fourier space:
![(Yang et al. 2021)](../images/HeterogeneousReconstruction/FRC.png)
$$
	FSC(k) = \frac{
		\sum_{s \in S_k} \hat{U}_s \hat{V}_s^*
	}{\sqrt{
		\left(
			\sum_{s \in S_k} | \hat{U}_s |^2
		\right)
		\left(
			\sum_{s \in S_k} | \hat{V}_s |^2
		\right)
	}}
$$
* $\hat{U}$ and $\hat{V}$: the 2 volumes being compared
* $*$:  conjugate of $\hat{V}$
	* So only the dot product of the real components is considered
* $S_k$: the set of voxels in a spherical shell at distance $k$ from the origin
	* Sum for voxels over a particular shell $S_k$
* Used as a diagnostic towards *consistency* of the reconstructed structures
* Resolution reported as $\frac{1}{k_0}$
	* $k_0 = \argmax_k FSC(k) < C$ in Å
	* $C$ is some fixed threshold
* Resolution can also be estimated by computing the FSC curve between the final reconstructed structure (using all data) and a density map simulated from the final atomic map with the threshold $C = 0.5$ Å
* Typically the background is zeroed out prior to computing the FSC curve
	* Avoid spurious correlations from background solvent density

**Gold standard FSC (GSFSC)**
* Independently reconstruct random halves of the dataset
* Compute the FSC curve between the aligned structures
* $C = 0.143$ Å

## References
1. Zhong, E. D. (2022). _Machine Learning for Reconstructing Dynamic Protein Structures from Cryo-EM Images_ (Doctoral dissertation, Massachusetts Institute of Technology).
2. Yang C, Jiao Y, Jian X, Cui Y. Image Deconvolution with Hybrid Reweighted Adaptive Total Variation (HRATV) for Optoacoustic Tomography. _Photonics_. 2021; 8(2):25. https://doi.org/10.3390/photonics8020025