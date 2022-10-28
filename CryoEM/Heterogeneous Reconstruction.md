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


## References
1. Zhong, E. D. (2022). _Machine Learning for Reconstructing Dynamic Protein Structures from Cryo-EM Images_ (Doctoral dissertation, Massachusetts Institute of Technology).