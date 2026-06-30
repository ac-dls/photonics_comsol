## Scattering Cross Sections for Gold Nanoparticle — COMSOL Multiphysics Model


## Overview

This repository contains a COMSOL FEM model of electromagnetic plane-wave scattering off a spherical **gold nanoparticle (100 nm diameter)**, computed
across the near-UV, visible, and near-infrared spectrum (~188–1940 nm). The model solves the full-wave Maxwell equations around a nanosphere and extracts the
**scattering, absorption, extinction, radar, and differential scattering cross-sections** (SCS, ACS, ECS, RCS, dSCS) as a function of incident wavelength, allowing identification of the particle's **localized surface plasmon resonance (LSPR)**.

This simulation was developed as part of my MSc research on carbon nanotube-based field
emission devices functionalized with gold nanoparticles, where understanding the optical
and electromagnetic response of the gold nanostructures is relevant to characterizing
their interaction with incident radiation and to interpreting optical characterization
data (e.g. UV-Vis extinction spectra) of the functionalized devices.

## Physical background

When light interacts with a metallic nanoparticle much smaller than the incident
wavelength, the free electrons in the metal can be driven into collective oscillation
by the oscillating electric field — a phenomenon known as a **localized surface plasmon
resonance**. For noble metals like gold, this resonance falls in the visible range and
produces a strong, narrow peak in the scattering and absorption cross-sections. This is
the same physical effect responsible for the vivid colors of gold colloidal solutions
and is widely exploited in biosensing, photothermal therapy, surface-enhanced spectroscopy,
and — in the context of this research — in tailoring the electromagnetic and electronic
response of functionalized nanostructured devices.

The underlying theory is **Mie scattering**: the exact analytical solution of Maxwell's
equations for a plane wave incident on a homogeneous sphere. The COMSOL model reproduces
this analytically known solution numerically via the finite element method, which allows
the same approach to be extended later to particles or arrangements that have no closed-form
analytical solution (non-spherical particles, particle clusters, particles near a substrate,
particles embedded in CNT-based structures, etc.).

## What was changed relative to the base model

This model is adapted from the COMSOL Application Exchange entry *"Mie scattering off
plasmonic nanoparticles"* by Romain Fleury (UT Austin), available at
[comsol.com/community/exchange/215](https://www.comsol.com/community/exchange/215/).
The base model computes the same set of cross-sections for a generic plasmonic or
dielectric nanosphere, validated against analytical Mie theory, using the gold optical
constants of Johnson and Christy (*Phys. Rev. B*, 1972).

For this version, the following parameters were modified:

| Parameter | Value used |
|---|---|
| Particle diameter | 100 nm |
| Particle material | Gold (Au) |
| Refractive index (n, k) | Tabulated, wavelength-dependent (Johnson & Christy data, loaded as interpolation functions) |
| Wavelength sweep range | ≈ 188 nm – 1940 nm |
| Surrounding medium | Ethylene glycol (n = 1.413) |

The real (n) and imaginary (k) parts of the gold refractive index were each imported into
COMSOL as a 1D **interpolation function** of wavelength, built from tabulated data
(49 wavelength points spanning the near-UV to near-IR), rather than using a fixed or
analytically fitted dielectric function. This preserves the fine spectral features of the
real material's optical response, including the characteristic onset of strong absorption
near 500 nm where gold's interband transitions begin to dominate.

## What the model computes

For each wavelength in the sweep, the model solves the frequency-domain wave equation
(electromagnetic waves, frequency domain — `emw` physics interface) around the gold
sphere enclosed in a spherical computational domain terminated by a perfectly matched
layer (PML), and evaluates:

- **SCS** — total Scattering Cross-Section
- **ACS** — Absorption Cross-Section
- **ECS** — Extinction Cross-Section (ECS = SCS + ACS)
- **RCS** — Radar Cross-Section (backscattering)
- **dSCS** — Differential Scattering Cross-Section (angular distribution of scattered light)

These cross-sections are obtained by integrating the Poynting vector (electromagnetic
energy flux) over surfaces enclosing the particle, following the standard Mie-theory
definitions, rather than reading values directly off a results table — this is the
specific contribution of the base model relative to the simpler COMSOL tutorial
"Optical Scattering off a Gold Nanosphere," which only computes the near-field pattern.

## Key results

- A clear **plasmon resonance peak** appears in the extinction and scattering spectra,
  consistent with the expected LSPR behavior of a ~100 nm gold sphere in air.
- The absorption cross-section dominates at shorter wavelengths (near-UV/blue), while
  scattering becomes increasingly significant at longer wavelengths, consistent with
  Mie theory scaling for particles of this size regime (intermediate between the
  small-particle/dipole limit and the large-particle/geometric-optics limit).
- The near-field plot of the out-of-plane electric field component (Ez) shows the
  expected dipolar field enhancement pattern around the particle at resonance.

*(See `results/` for exported figures and `report/` for the full written analysis, if included.)*

## Model details

- **Software**: COMSOL Multiphysics (version 6.2)
- **Physics interface**: Electromagnetic Waves, Frequency Domain (`emw`)
- **Geometry**: 3D, single sphere (100 nm diameter) centered in a spherical air domain,
  enclosed by a Perfectly Matched Layer (PML) to absorb outgoing scattered radiation
  without artificial reflections
- **Material data**: Gold optical constants (n, k) entered as interpolation functions
  built from tabulated wavelength-dependent data
- **Study**: Frequency/wavelength sweep across the range listed above
- **Validation**: Results benchmarked against analytical Mie theory predictions (as
  established in the original base model)

## File structure

```
├── model/
│   └── au_nanoparticle_100nm_mie_scattering.mph
├── results/
│   ├── total_cross_sections.png
│   ├── plasmonic_resonance_Ez.png
│   └── (additional exported plots)
├── data/
│   └── gold_n_k_johnson_christy.csv   ← refractive index data used as interpolation input
└── report/
    └── (optional written analysis / thesis excerpt)
```

## Context and motivation

This model is part of the broader simulation work conducted during my MSc research at
COPPE/PENt (UFRJ), which focused on carbon nanotube field emission devices functionalized
with gold nanoparticles, modeled computationally via DFT and FEM for application to ion
gridded thrusters. While the primary electron emission and structural simulations of that
work were performed with other tools, this COMSOL model was used to independently
characterize the optical/electromagnetic signature of the gold nanoparticles themselves —
relevant background for interpreting the role of the gold functionalization beyond its
electronic effects.

## References

- P. B. Johnson and R. W. Christy, "Optical Constants of the Noble Metals," *Phys. Rev. B*,
  vol. 6, no. 12, pp. 4370–4379, Dec. 1972.
- R. Fleury, "Mie scattering off plasmonic nanoparticles," COMSOL Application Exchange,
  entry 215. [comsol.com/community/exchange/215](https://www.comsol.com/community/exchange/215/)
- G. Mie, "Beiträge zur Optik trüber Medien, speziell kolloidaler Metallösungen,"
  *Annalen der Physik*, vol. 330, no. 3, pp. 377–445, 1908.

## License

MIT License.
