## Experiment summary
1. A purified solution of the molecule of interest is frozen in a thin layer of vitreous ice in a random orientation.
2. Exposed to an electron beam
	1. Molecules' electron scattering potential (the volume $V$) interacts with an electron beam imaged on a detector
3. Randomly oriented projections of the electrons scattering potential $V$ are imaged on a detector.
	1. Raw *micrographs*
4. Algorithms for analysis
	1. Unknown poses estimation of each particle extracted from the micrographs
		1. orientation
		2. centering shift
	2. Volume reconstruction:
		1. A tomographical problem
			1. backprojection-based methods
			2. compressive sensing-style solvers (?)

## CryoEM Images
* Size on the order of $100^2 - 400^2$  pixels
* Challenges in volume reconstruction:
	1. Poses unknown for all 2-D projections
	2. Very low SNR ratio: ~-20 dB (what does this measurement signify?)
	3. molecules in a sample can deform, and frozen in various unknown conformations

## Homogeneous Reconstruction

## Heterogenenous Reconstruction
* Most methods in this category assume the poses to be known (from consensus reconstruction?)
* Variable of interest is the description of the conformational state and its associated $V$
* Consider the deformations of the molecules
* Reconstruct a discrete set or a low-dimensional manifold of conformations

