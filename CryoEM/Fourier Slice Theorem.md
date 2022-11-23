## Fourier Slice Theorem
The Fourier transform of a 2-D projection of $V$ is a 2-D slice through the origin of $V$ in the Fourier domain, that is, the amplitude and phase of each point in the 2-D Fourier projection have the same values as that of each point in the slice through the origin of the Fourier space at the same angle. **The slice is perpendicular to the axis of projection**:
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

## References
1. Zhong, E. D. (2022). _Machine Learning for Reconstructing Dynamic Protein Structures from Cryo-EM Images_ (Doctoral dissertation, Massachusetts Institute of Technology).