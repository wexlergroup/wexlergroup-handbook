# Choosing an Energetics Engine

Whether your question concerns **thermodynamics** (energies of end‑states) or **kinetics** (energetic profile along a pathway), you must select a _total‑energy engine_—the algorithm that evaluates the potential energy of a given atomic configuration. Energy engines fall into two broad families:

***

## Quantum‑Mechanical (QM) Engines

QM engines approximate solutions to the electronic Schrödinger equation, returning the ground‑state energy and, often, the electronic eigenvalue spectrum (molecular orbitals in molecules, bands in solids).

* **Wave‑function methods**—Hartree–Fock, Møller–Plesset perturbation theory, coupled cluster \[e.g., CCSD(T)]—treat electrons explicitly and deliver very high accuracy at steep scaling.
* **Density‑functional theory (DFT)** collapses the many‑electron wavefunction into the electron density (three variables instead of ). Exact in principle, DFT relies in practice on approximate exchange–correlation (XC) functionals (GGA, meta‑GGA, hybrid, range‑separated, etc.). Ground‑state energies are often accurate to 1–2 kcal mol⁻¹; excited‑state predictions depend strongly on the functional.
* **Beyond‑DFT corrections**—time‑dependent DFT, GW, Bethe–Salpeter, multireference approaches—extend QM engines to spectra, strong correlation, and charge transport.

**Pros**: systematic accuracy; transferable; minimal empirical parameters.\
**Cons**: computationally expensive; poor scaling with system size; errors inherit from XC approximations.

***

## Classical (Force‑Field) Engines

Classical engines replace the electronic problem with an explicit functional form for interatomic interactions:

* **Traditional empirical force fields** (e.g., Lennard‑Jones, CHARMM, EAM, ReaxFF) are parameterized against experiment or QM data and enable nanosecond–microsecond molecular dynamics of atoms.
* **Machine‑learning (ML) potentials**—Behler–Parrinello neural networks, GAP, SNAP, E(3)‑equivariant graph neural networks—interpolate QM reference data with near‑DFT accuracy at classical cost. Successful deployment demands careful training‑set design, regularization, and uncertainty quantification.

**Pros**: orders‑of‑magnitude faster; suitable for large systems and long timescales.\
**Cons**: accuracy limited by functional form or training data; transferability narrower than QM.

***

## Choosing Between QM and Classical Engines

| Criterion            | QM Engines                                      | Classical Engines                          |
| -------------------- | ----------------------------------------------- | ------------------------------------------ |
| System size          | ≤ 10³ atoms                                     | 10³–10⁶ atoms                              |
| Accessible timescale | femtoseconds–nanoseconds                        | nanoseconds–microseconds (empirical)       |
| Key outputs          | Energies, electronic structure, accurate forces | Energies, forces, thermodynamics, kinetics |
| Setup effort         | Choose functional/basis; no fitting             | Parameterization or ML training required   |

### Practical Guidelines

1. **Benchmark accuracy first**: perform QM calculations on small models and use them to validate or train classical/ML potentials.
2. **Scale up judiciously**: once accuracy is acceptable, employ classical or ML engines for extensive sampling, free‑energy maps, or kinetics.
3. **Hybrid approaches**: QM/MM partitioning, on‑the‑fly ML potentials, and -learning combine the strengths of both families.

> _Rule of thumb_: invest in the most accurate engine you can afford **for the property that controls the decision you care about**—then choose the fastest engine that reproduces that accuracy.
