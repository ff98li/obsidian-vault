## Current challenges

## Goal
* Continuous **heterogeneous** reconstruction
* A general approach for analysing structural heterogeneity in macromolecular complexes of varying size and *expected sources of heterogeneity*
* $ab\ initio$ 
## Methods
* Neural network to learn heterogeneous ensembles of cryo-EM density maps
*


## Innovations

### Existing works for hetergeneous reconstruction
* *Discrete classification* approach:
	* make limiting assumptions on the observed structural heterogeneity
		* heterogeneity is modeled as though it originates from a small number of independent, discrete states
	* implemented as *3D classification* or *heterogeneous refinement* in many cryo-EM software packages
		* Require specifying initial models(?) for refinement
	* error-prone and omits potentially relevant structures because the number + nature of the underlying structural states is unknown a priori
	* ill-suited for reconstructing structures undergoing continuous conformational changes

#### RELION
* Multibody refinement
	* models the structure as the sum of user-defined rigid bodies allowed to rotate relative to one another
		* Place structural assumptions on the observed heterogenity

#### CryoSPARC
* 3D Variability Analysis
	* linear subspace model, PCA-based approach to describe *continuous heterogeneity*
	* the visualised heterogeneity contains artifacts when linear interpolations along basis volumes poorly approximate the conformational deformation of a molecule (?)

#### CryoDRGN

* manifold embedding approach?

## Limitations
**Under what circumstances will their method fail?**


**?**: need more explanation