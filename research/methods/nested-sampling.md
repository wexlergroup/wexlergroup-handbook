# Nested Sampling

### Introduction

Nested sampling is a computational approach used to estimate the Bayesian evidence by exploring typically high-dimensional parameter spaces with a set (a liveset) of walkers (live points).  Nested sampling iteratively replaces the walker with the lowest likelihood with a new walker with higher likelihood, thus efficiently mapping out the likelihood landscape.

### Application in material sciences

Nested sampling has been applied to studies of phase transitions of nanoparticles and surfaces. The typical parameter spaces explored by nested sampling include phase spaces (momentum-position spaces) as well as configurational spaces (potential energy surfaces).&#x20;

### Algorithm

Step 0: Draw a set of random walkers, i.e., configurations, uniformly distributed in the phase space.&#x20;

* Step 1: Find the walker with the highest energy (lowest likelihood), record the energy as the energy ceiling.
* Step 2: Replace the highest-energy walker with a new random walker with a lower energy.
* Step 3: Restart from Step 1.

### Implementations

* [pymatnest](https://github.com/libAtoms/pymatnest): a Python+Fortran implementations
* [FreeBird.jl](https://github.com/wexlergroup/FreeBird.jl): a Julia package



