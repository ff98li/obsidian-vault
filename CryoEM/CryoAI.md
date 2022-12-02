## Current challenges
* To estimate ...
	* the unknown 3D poses
	* the *3D electron scattering potential* of a biomolecule from noisy 2D images
* Primary bottleneck of existing works for cryo-EM reconstruction algorithms
	* **orientation matching**
		* Long computation time, poorly scaled with increasing dataset sizes

## Goal
* $ab\ initio$ reconstruction for **homogeneous** conformations/homogenous reconstruction
	* heterogenous reconstruction can then be performed with the estimated poses by other cryo-EM software with this functionality such as RELION, CryoSPARC or CryoDRGN
 
## Methods
**The approach they purposed to solve the problem**
* Direct gradient-based optimisation to jointly estimate poses and the electron-scattering potential of a non-deformable molecule
* **Encoder**
	* A discriminative model
	* A mapping from images to poses: predicts a pose of each partical image
* **Decoder**
	* A generative physics-based pipeline
	* uses the predicted poses and <u>a description of the volume</u> to predict an image (rendering?)
	* aggregate each particle image into an implicit representation of the scattering potential volume
	* parameterised $V$ in a neural network
* Volumes stored in the Fourier domain for <u>computational efficiency</u>
* Modern coordinate network architecture for <u>memory efficiency</u>
* Symmetric loss function

## Innovations
### Exisiting works
* Iteratively alternates between ...
	* updating $V$ 
	* the estimated poses: *oritentation matching*
		* computational expensiv
		* Requiring an exhaustive search in a 5-D space for each particle
			* $\phi_i \in SO(3) \times \mathbb{R}^2$: A pose for particle $i$, defined by $[\mathbf{R}_i\ \mathbf{t}_i]$
			* $\mathbf{R}_i \in SO(3) \subset \mathbb{R}^{3 \times 3}$: Rotation matrix
			* $\mathbf{t}_i \in \mathbb{R}^2$ : translocation (why 2-D?)
* Existing heterogeneous methods assume the poses to be known
* Two categories of existing works classified by the author:

#### Non-amortised inference methods
* Posterior distribution for the poses $p( \phi_i | Y_i, V)$ is computed independently for each image $Y_i$
* Common-line approaches
* Projection-matching strategies
* Bayesian formulation
* [A helpful read for understanding the methods described above](https://arxiv.org/abs/2003.13828)

##### RELION
* Bayesian approach, MAP by EM to compute the posterior distributions for the poses

##### CryoSPARC
* MAP optimisation jointly using ...
	* SGD to optimise $V$
		* overcome costly updates in the EM approach of RELION
		* [*Ullrich .et al*](https://arxiv.org/abs/1906.07582) showed that optimising the posterior distribution for poses by SGD fails due to the high convexity of the problem (does it have to do with their choice of the Fourier domain?)
	* **branch-to-bound** algorithm to estimate $\phi_i$
		* Accelerates estimation of poses for each $Y_i$ with the estimated $V$
* Parameterise $V$ with a 3-D voxel array

##### CryoDRGN
* Use a coordinate-based representation to approximate the electron scattering function in Fourier space
* Input for neural representation
	* 3-D Fourier coordinates
	* a latent vector encoding the conformational state
* Account for continuous deformations of a molecule
* Still assume the poses to be determined by a consensus reconstruction (CryoSPARC or RELION)

##### CryoDRGN-BNB and CryoDRGN2
* Poses are jointly estimated with $V$
* Multi-resolution approach
* The global 5-D pose search still remains the most computational expensive step

#### Amortised inference methods
* Learn a parameterisd $q_\xi(Y_i)$ that approximates $p( \phi_i | Y_i, V)$
	* Avoid the main bottleneck of the orientation matching step at the expense of optimising $\xi$.
* Distributions for poses and conformational states are predicted by VAE
* Decoder uses the predicted conformation to deform a base <u>backbone frame</u> of Gaussian blobs, and uses the predicted pose to produce a projection of the blobs (?)
* Requires *a priori* information about the backbone frame
* [CryoVAEGAN](https://arxiv.org/abs/1911.08121)
* [Rosenbaum *et al.*](https://arxiv.org/abs/2106.14108)
* [CryoPoseNet](https://ieeexplore.ieee.org/document/9607470)
	* A non-VAE framework that can perform homogenous reconstruction with a random initialisation of $V$
		* avoid the need for prior information about the molecule
		* demonstrated the possibility of using non-VAE to predict $\mathbf{R}$
	* $V$ stored in real space in the decoder
	* image formation model in Fourier space
		* Requires a 3-D Fourier transformation at each forward pass (slow)
	* Reconstructed $V$ often gets stuck in local minima
* Tested with simulated datasets, no amortised inference methods for $V$ estimation from unknown $\phi_i$ have been proven to work with real cryo-EM datasets.

### CryoAI
* does not assume the poses to be known
* The image formation model is simulated in Fourier space
	* avoid approximating the integrals via the Fourier-slice theorem
		* an advantage over CryoDRGN?
* Its learned image-to-pose mapping avoids the oritentation matching step
	* bottleneck of the existing algorithms
	* amortised inference of poses to circumvent the need for expensive searches over $SO(3) \times \mathbb{R}^2$
	* no parameters statistically associated with each image
		* hence memory footprint and runtime does not scale with the number of images in the dataset
* A scalable approach: amortises over the size of the dataset
* One order of magnitude faster runtime than the SoTA methods
* Address CryoPoseNet's problem of getting stuck in local minima when reconstructing $V$
	* symmetric loss function

### Symmetric Loss
$$
	\mathcal{L}_{sym} (Y_i, \hat{Y}_i) =
	\sum_{i \in \mathcal{B}} \min \left\{
		\lVert \hat{Y}_i - \Gamma_{\xi, \theta} (Y_i) \rVert^2_2, \
		\lVert
		\mathcal{R}[\hat{Y}_i] - \Gamma_{\xi, \theta} (\mathcal{R}[Y_i])
		\rVert^2_2
	\right\}
$$


## Limitations
**Under what circumstances will their method fail?**