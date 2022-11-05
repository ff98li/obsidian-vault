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
	2. Very low *signal-to-noise ratio (SNR)*: ~-20 dB
	3. molecules in a sample can deform, and frozen in various unknown conformations

## Computational Pipeline
1. Micrograph preprocessing
	1. Preprocessing
		1. Beam-induced motion correction
		2. Estimation of CTF parameters
	2. Segmenting
		1. Bounding boxes containing individual molecules are identified and extracted
			1. *Particle picking*
				1. *Cross-correlation*
	3. Optional: 2-D classification
		1. Clustering algorithms to sort segmented images into common viewing directions
			1. Hierarchical Asendant Classification
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

### TEM Imaging
![Microscope architecture](../images/ImageFormationModel/tem_architecture.png)

### Electron Wave Function
The wave function of an electron can be written as $\psi(\mathbf{r}, t) = \exp(ik\mathbf{r})\exp(-iwt) = A(\mathbf{r})B(t)$. We assume the electrons are in a stationary state (independent of time) and consider only $A(\mathbf{r})$. The the electron spatial frequency (propogation constant) is given by $k = \frac{2\pi}{\lambda}$.
Common cryo-EM voltage:
| Energy (keV) | Wavelength (Å) |
|:------------:|:--------------:|
|      120     |      0.033     |
|      200     |      0.025     |
|      300     |      0.020     |

As it leaves the condenser lens system, we assume the incident electron to be a monochromatic plane wave. The incident electron wave function is then given by $A(\mathbf{r}) = \exp(ik\mathbf{r} \cdot \mathbf{u}_z) = \exp(ikz) = A_{\text{incident}}(z)$.
At $z = 0$ where our specimen is, $A_{\text{scattered}}(\mathbf{r}) = A_{\text{incident}}(z = 0) \cdot \exp(i \epsilon \phi(x, y))$, where $\epsilon \phi(x, y)$ is the phase object.
Recall the Taylor expansion of $e^x$ is given by $e^x = \sum^{\infty}_{n=0}\frac{x^n}{n!}$. When $x << 1$, $e^x \approx \sum^2_{n=0} \frac{x^n}{n!} = 1 + x$. We assume the phase to be weak with $\epsilon << 1$. Then 


### Scattering Events
Due to the duality of electrons, they can act as either particles or waves.


## References
1. Zhong, E. D. (2022). _Machine Learning for Reconstructing Dynamic Protein Structures from Cryo-EM Images_ (Doctoral dissertation, Massachusetts Institute of Technology).
2. [slaclab](https://github.com/slaclab)/**[cryoEM-notebooks](https://github.com/slaclab/cryoEM-notebooks)**