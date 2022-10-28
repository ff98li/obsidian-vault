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

## References
1. Zhong, E. D. (2022). _Machine Learning for Reconstructing Dynamic Protein Structures from Cryo-EM Images_ (Doctoral dissertation, Massachusetts Institute of Technology).