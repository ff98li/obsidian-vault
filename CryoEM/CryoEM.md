## Experiment summary
1. A purified solution of the molecule of interest is frozen in a thin layer (prevents from inelastic scattering events) of vitreous ice in a random orientation.
2. Exposed to an electron beam
	1. Molecules' electron scattering potential (the volume $V$) interacts with an electron beam imaged on a detector
	2. Most electrons pass through the sample **uninterrupted**, contributing to the *shot noise* in the recorded images.
		1. Elastically scattered electrons
		2. Contribute to observed contrast in the recorded cryo-EM image
	3. Some inelastic scattered electrons are filtered out before reaching the detector
		1. causing radiation damage
3. Randomly oriented projections of the electrons scattering potential $V$ are imaged on a detector.
	1. Raw *micrographs*
4. Compuational pipeline for analysis
	1. Unknown poses estimation of each particle extracted from the micrographs
		1. orientation
		2. centering shift
	2. Volume reconstruction:
		1. A tomographical problem
			1. backprojection-based methods
			2. compressive sensing-style solvers (?)

## CryoEM Images
* Size on the order of $100^2 - 400^2$  pixels
* $10^4 - 10^7$ recorded images
* The imaging process magnifies the object of interest by 5-6 orders of magnitude
	* Capture snpshots of single particles
		* Spatial scale of each pixel spans around 1 Å
		* Theoretical limit given by wavelength of the electron beam: 0.02 Å at 300 kV due to limitation from radiation damage
* Challenges in volume reconstruction:
	1. Poses unknown for all 2-D projections
	2. Very low SNR ratio: ~-20 dB (what does this measurement signify?)
	3. molecules in a sample can deform, and frozen in various unknown conformations

## Computational Pipeline
1. Micrograph preprocessing
	1. Preprocessing
		1. Beam-induced motion correction
		2. Estimation of CTF parameters
	2. Segmenting
		1. Bounding boxes containing individual molecules are identified and extracted
			1. *Particle picking*
	3. Optional: 2-D classification
		1. Clustering algorithms to sort segmented images into common viewing directions
		2. Particles imaged from consistent viewing directions will be averaged together
			1. Visually inspected for quality and structural features of interest
2. 3-D reconstruction
	* Goal: recover the 3-D structure i.e. density map i.e. volume $V$ of the target protein
		* $V: \mathbb{R}^3 \to \mathbb{R}$
			* $V(\mathbf{x})$ gives the electron scattering potential at a point in space $\mathbf{x} \in \mathbb{R}^3$
			* estimated from a set of noisy projection images $\{X_1, \ldots, X_N\}$ in unknown orientation
	* Homogeneous reconstruction
		* Reconstruct 3-D cryo-EM density volume
	* Heterogeneous reconstruction
		* Reconstruct a distribution for volumes
	* Involves multiple rounds of 3-D classification on subsets of the images
3. Atomic model building

## Image Formation Model
The generation of image $X: \mathbb{R}^2 \to \mathbb{R}$ can be modeled as
$$
\begin{align*}
	X(r_x, r_y) &= g\ *\ T_t \int_{\mathbb{R}} V(R^\top \mathbf{r})dr_z + \epsilon\\
	\mathbf{r} &= [r_x,\ r_y,\ r_z]^\top
\end{align*}
$$
* $V$: the density volume
* $\mathbf{R} \in SO(3)$: the 3-D rotation group, an unknown orientation of $V$
* $T_t$: an unknown in-plane translation by $t \in \mathbb{R}^2$
	* corresponding to imperfect centering of the volume within the image
* $g$: the point spread function for the microscope before being corrupted with additive noise and registered on a discrete grid of image size $D \times D$
* The image signal is convolved with $g$
* WTF?

## Fourier Slice Theorem
The Fourier transform of a 2-D projection of $V$ is a 2-D slice through the origin of $V$ in the Fourier domain. The slice is perpendicular to the axis of projection:
$$
\mathcal{F}_2 P_2 = \mathcal{S}_2 \mathcal{F}_3
$$
* $\mathcal{F}_n$ is the Fourier transform in $n$ dimensions
* $P_2$ is the projection operator from 2-D to 3-D
* $\mathcal{S}_2$: the slice operator
	* extracts a 2-D central slice perpendicular to the axis of projection

In the Fourier domain, the generative process for image $\hat{X}$ from $\hat{V}$ can be written:
$$
	\hat{X} = \hat{g} S_t A_R \hat{V} + \epsilon
$$
* $\hat{g} = \mathcal{F}g$: CTF
* $S_t$: a phase shift operator corresponding to image translation by $t$ in real space
* $A_R \hat{V} = \hat{V} (\mathbf{R}^\top [\cdot,\ \cdot,\ 0]^\top)$: a linear slice operator corresponding to rotation $\mathbf{R}$ and linear projection along the $z$-axis in real space
* $\epsilon$: frequency-dependent noise modelled as independent, zero-centred Gaussian noise in Fourier space

Under this model, the probability of observing an image $\hat{X}$ with pose $\phi = [\mathbf{R}\ \mathbf{t}]$ from $\hat{V}$:
$$
	p( \hat{X} | \phi, \hat{V} )
	= p( \hat{X} | \mathbf{R}, \mathbf{t}, \hat{V} )
	= \frac{1}{Z} \exp
	\left\{
		\sum_{l} \frac{-1}{2\sigma^2}
		\lvert
			\hat{g}^{(l)}A_R^{(l)} \hat{V} - S_t^{(l)} \hat{X}^{(l)}
		\rvert^2
	\right\}
$$
* $l$: a 2-component index over Fourier coefficients for the image
* $Z$: normalisation constant

## Contrast Transfer Function (CTF)
* $\hat{g}: \mathbb{R}^2 \to \mathbb{R}$
	* A function of the spatial frequency vector $\mathbf{k} = [k_x,\ k_y]$ that approximates the **signal transfer** as an electron beam travels through the microscope and passes through a weak object
* Describes the phase shift incurred by the microscope's abberations
	* defocus settings $(z_1, z_2, z_\theta)$
	* spherical aberration $C_s$
* The **wave function** of the observed contrast experiences a **phase shift** upon passing through the sample that can be indirectly measured by the detector.

A commonly used form of the CTF:
$$
\begin{align*}
	\hat{g}(\mathbf{k})
	&= \sqrt{(1 - w^2)} \sin(\gamma(\mathbf{k}))
	- w \cos(\gamma(\mathbf{k})) \\
	& \\
	\gamma(\mathbf{k})
	&= 2\pi \left(
		- \frac{ z(\mathbf{k}) \lambda |\mathbf{k}|^2 }{2}
		+ \frac{ C_s \lambda^3 |\mathbf{k}|^2 }{4}
	\right) - \phi_0
\end{align*}
$$
* $\lambda$: the wavelength of the electron
* $C_s$: the spherical aberration of the objective lens
* $\phi_0$: additional phase shift from a phase plate
* $z(\mathbf{k})$: the defocus of the microscope:
$$
\begin{align*}
	z(\mathbf{k}) &= 0.5 \left(
		z_1 + z_2 + \left(
			z_1 - z_2
		\right) \cos\left(
			2 (\theta - z_\theta)
		\right)
	\right) \\
	\theta &= \arctan^2(\frac{k_y}{k_x})
\end{align*}
$$

* $z_1$, $z_2$: defocus parameters
* $z_\theta$: astigmatism

Finally, an envelope function models the attenuation of the signal at high frequencies:
$$
	\hat{g}' = \hat{g}(\mathbf{k})e^{-B|\mathbf{k}| / 4}
$$
* $B$: B-factor

These parameters are determined by the microcopy settings, some of which are estimated by function fitting software prior to 3-D reconstruction.

* The degree of phase shift is determined by
	1. sample
	2. frequency-dependent interference
* The microscope-dependent signal transfer can be derived from weak phase-object approximation as the CTF.

https://youtu.be/mPynoF2j6zc

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
	* Bias from $V^{(0)}$
	* Artifacts in $V$ due to the high noise level in the images and local convergence properties of iterative refinement (highly non-convex objective)
* Current standard:
	* 2-fold cross-validation based on the **Fourier Shell Correlation** curve

The FSC curve meaasures correlation between volumes as a function of **spherically-averaged radial shells** in Fourier space:
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
* $S_k$: the set of voxels in a spherical shell at distance $k$ from the origin
* Used as a diagnostic towards *consistency* of the reconstructed structures
* Resolution reported as $\frac{1}{k_0}$
	* $k_0 = \argmax_k FSC(k) < C$
	* $C$ is some fixed threshold
* Resolution can also be estimated by computing the FSC curve between the final reconstructed structure (using all data) and a density map simulated from the final atomic map with the threshold $C = 0.5$
* Typically the background is zeroed out prior to computing the FSC curve
	* Avoid spurious correlations from background solvent density

**Gold standard FSC (GSFSC)**
* Independently reconstruct random halves of the dataset
* Compute the FSC curve between the aligned structures
* $C = 0.143$

## Heterogenenous Reconstruction
* Variable of interest is the description of the conformational state and its associated $V$
* Algorithms relax the assumption in the image formation model
	* *Each image captures an identical, static sturcture*
* Take into account structural variation between the particles and aim to reconstruct a distribution of $V$
* Consider the deformations of the molecules

### Discrete Models
#### Multiclass Refinement
* Simultaneously reconstruct $K$ independent volumes for given sample heterogeneity
* Extended assumption for the image formation model:
	* Images are generated from $V_1,\ \ldots,\ V_K$ independent volumes
	* Inference requires marginalisation over $\phi_i$ and *class assignment probabilities* $\pi_j$
$$
	\argmax_{V_1, \ldots, V_K} \sum^N_{i = 1} \log \sum^K_{j = 1} \left(
		\pi_j \int p(X_i | \phi, V_j) p(\phi) d\phi
	\right) +
	\sum^K_{j = 1} \log p(V_j)
$$
* Resolving such discrete heterogeneity is handled through <u>a hierarchical approach</u>
	* Refine subsets of the imaging dataset with manual choices for the number of classes $K$ and for the initial models for refinement
	* Many rounds of expert-guided processing
	* There exists an upper limit to the number of resolvable structures:
		* Each class has a minimum number of images to provide sufficient views for 3-D reconstruction and to produce high resolution structure
			* Bias-variance trade-off
* Error-prone due to the number and the unknown nature of the underlying structural states
	* Overfitting of some conformations
	* Missing other states
	* Blurring artifacts from averaging together continuous forms of heterogeneity
	
#### Masked Refinement
* **Focused refinement** / **Focused classification**
	* use <u>masking</u> to focus on specific regions of the structure
	* In the E-step of iterative refinement, poses are estimated by aligning images to the <u>masked</u> volume
* Requires a user-defined mask on the region of interest from an <u>upstream consensus homogeneous reconstruction</u>
* Assumption:
	* *The region of focus is rigid, though its orientation may change relative to the rest of the complex*
* In practice, there are **SNR** limitations in focusing on regions that are <u>too small to provide enough signal</u> for alignment
* Issue in standard focused refinement approach: <u>mismatch between the image and the estimate of the volume</u>
	* Image: contains the full complex
	* Estimate of the volume: contains the masked region
	* Solution: **Focused refinement with signal subtraction**
		* A second masked is defined around the region of the volume that should be <u>excluded</u> in alignment
			* e.g. a micelle in a membrane protein complex

### Continuous Models