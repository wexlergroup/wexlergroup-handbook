---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Coordination Complex Enumeration

## 1. Why transition-metal enumeration is tricky

Metal–ligand bonding introduces variable coordination numbers, geometries (octahedral, tetrahedral, square-planar, etc.), spin states, and oxidation states. In contrast, organic ligands add rotatable bonds, ring puckers, and, often, multiple binding modes. A practical enumerator, therefore, has to:

1. **Handle metal-centred polyhedra** (not just organic covalent graphs).
2. **Produces stereoisomers** (mer/fac, Δ/Λ, cis/trans, etc.).
3. **Generate a large conformer ensemble** for each stereoisomer.
4. **Interface with electronic-structure codes** so you can obtain energies, spectra, charges, etc., and perform Boltzmann re-weighting.

***

## 2. High-level workflow

| Step                             | Tool choices                                                                 | One-line rationale                                                                                |
| -------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Build metal–ligand graphs**    | `molSimplify`, `stk`, `Molassembler`                                         | Purpose-built for transition-metal topologies and stereoisomer enumeration.                       |
| **Expand to 3-D conformers**     | `CREST/pycrest`, `RDKit`, `Molassembler`, `stk.ConstructedMolecule.Optimize` | Systematic + meta-dynamics searches; handles large, flexible ligands.                             |
| **Single-point energy/gradient** | `xTB` (via `pyxtb`), ORCA/Gaussian via `QCEngine`, `ASE` calculators         | Fast enough for hundreds–thousands of conformers.                                                 |
| **Ensemble statistics**          | `numpy/pandas` or `QCPortal`                                                 | Compute $$\langle A \rangle = \sum A_i w_i / \sum w_i$$ with $$w_i = e^{-E_i / k_{\text{B}} T}$$. |

***

## 3. Python libraries that do the heavy lifting

### **3.1. molSimplify** (MIT Kulik group)

_Automated transition-metal complex builder and enumerator._ Accepts a metal ion, oxidation/spin state, ligand SMILES/XYZ files, and user-defined coordination geometry; spits out all stereoisomers and writes 3-D structures you can pass directly to xTB/DFT.

<a href="https://github.com/hjkgrp/molSimplify" class="button primary">GitHub</a>    <a href="https://hjkgrp.mit.edu/tutorials/2016-06-18-molsimplify-tutorial-1-structure-generation/molSimplify_v1.pdf" class="button primary">User Manual</a>    <a href="https://hjkgrp.mit.edu/molsimplify-tutorials/" class="button primary">Tutorials</a>

Key features:

* `--first_coord` / `--second_coord` flags let you mix ligands.
* `--enum` option turns on automatic mer/fac, Δ/Λ, cis/trans enumeration.
* Python API (`from molSimplify import mol3D`) allows in-memory generation without disk I/O.

### **3.2. stk (Supramolecular Toolkit)**

Modular construction kit that treats _building blocks_ (ligands, metal centers) and _topology graphs_ (octahedron, tetrahedron, square-pyramidal, etc.) as first-class objects. It can loop over every ligand combination and call RDKit or its optimizer for an initial 3-D guess.

<a href="https://github.com/lukasturcani/stk" class="button primary">GitHub</a>    <a href="https://doi.org/10.1063/5.0049708" class="button primary">Paper</a>

Highlights:

* `stk.MetalComplex` supports arbitrary denticities and even polynuclear nodes.
* Built-in database writer so you can save each complex plus metadata for downstream ML.
* Seamless conformer search with `stk.ConstructedMolecule.with_centroid_minimization()`.

### **3.3. Molassembler**

Graph-based engine that enumerates stereoisomers, _including_ transition-metal stereochemistry, and comes with Python bindings. It can generate complete conformer ensembles for each isomer and export to RDKit or xyz.

<a href="https://doi.org/10.1021/acs.jcim.0c00503" class="button primary">Paper</a>

_Strength:_ handles even edge- and face-capped polyhedra.

### **3.4. Conformer engines**

| Package                                                                                                                                                                                                                          | Notes                                                                          | Best use case                                                                           |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------- |
| [**CREST**](https://crest-lab.github.io/crest-docs/) **+ `pycrest`**                                                                                                                                                             | Exhaustive conformational sampling with meta-dynamics; good for floppy ligands | <p>Python wrapper enables asynchronous job submission; returns ranked ensemble.<br></p> |
| [**RDKit**](https://www.rdkit.org/docs/index.html) **(`EmbedMultipleConfs`)**                                                                                                                                                    | Quick, MMFF-based conformer seeds                                              | <p>Solid for small ligands or as seeds before CREST.<br></p>                            |
| [**Molassembler**](https://doi.org/10.1021/acs.jcim.0c00503)**/stk internal optimizers**                                                                                                                                         | Topology-aware initial guesses                                                 | <p>Already metal-aware, so fewer busted geometries.<br></p>                             |
| [**py-conformational-sampling**](https://github.com/ZimmermanGroup/py-conformational-sampling)**/** [**Ringo**](https://github.com/TheorChemGroup/Ringo)**/**[**conformer\_rl**](https://github.com/ZimmermanGroup/conformer-rl) | Exotic search strategies (inverse kinematics, RL) for very flexible ligands    | <p>Plug-and-play with RDKit graphs.<br></p>                                             |
| [**AMS Conformers**](https://www.scm.com/doc/AMS/Utilities/Conformers.html)                                                                                                                                                      | GUI-friendly wrapper that couples RDKit and CREST from Python                  | <p>We license AMS.<br></p>                                                              |

### **3.5. Electronic-structure back-ends**

* **xTB** via `pyxtb` or the [`ASE`](https://wiki.fysik.dtu.dk/ase/index.html) calculator for ultra-fast GFN2-xTB energies.
* **ORCA, Gaussian, Q-Chem** via `QCEngine` so you can call high-level DFT in the same loop.
* All integrate with `molSimplify` and `stk` job managers out of the box.

***

## 4. Putting it all together – a minimal recipe

```python
from molSimplify.Classes.mol3D import mol3D
from pycrest import crest
import numpy as np

mn_complex = mol3D()                         # 1) build
mn_complex.auto_build(metal='Mn', oxstate=2,
                      ligands=['Cl', 'pyridine', 'pyridine', 'Cl', 'MeCN', 'H2O'],
                      geometry='oct')        # enumerate stereoisomers
all_isomers = mn_complex.enum_isomers()

ensemble = []
for iso in all_isomers:                      # 2) conformer sampling
    xyz = iso.write_xyz()                    #   (in-memory string)
    c = crest.ConformerSearch(xyz, nprocs=4) #   CREST defaults
    ensemble.extend(c.get_ensemble())        #   energies in kcal/mol

# 3) Boltzmann average of any property, e.g., dipole moment
kT = 0.001987204 * 298.15                    # in kcal/mol
weights = np.exp(-np.array([m['E'] for m in ensemble]) / kT)
dipoles = np.array([m['props']['dipole'] for m in ensemble])
avg_dipole = (dipoles * weights).sum() / weights.sum()
print(f"Boltzmann-weighted ⟨μ⟩ = {avg_dipole:.2f} D")
```

Replace the ligand list with every permutation you want (`itertools.product` is your friend) and loop over oxidation/spin states as needed.

***

## 5. Practical tips

1. **Filter early.** Enumerating every ligand permutation × stereoisomer × conformer explodes combinatorially. Use simple chem-informatic filters (e.g., denticity, steric clash score, heuristic binding energies) before expensive CREST or DFT calls.
2. **Parallelize.** All listed libraries are thread- or MPI-aware, and `stk` ships with a MongoDB job store so you can resume interrupted runs.
3. **Check spin states.** `molSimplify` can produce high-spin, intermediate, and low-spin configurations.
4. **Validate coordination geometries.** `Molassembler` lets you enforce specific polyhedral templates to avoid unrealistic distortions after geometry optimization.
5. **Property calculations.** For ensemble averages of spectroscopic observables, [`xTB`](https://xtb-docs.readthedocs.io/en/latest/index.html)'s Δ-learning corrections (GFN2-xTB-SOPA) are often within chemical accuracy at a fraction of DFT cost—handy when you have thousands of conformers.

***

## 6. When brute-force enumeration breaks down

If you find that the number of possible configurations is still too large:

1. **Use guided searches** like conformer-RL or genetic algorithms in `stk` to bias toward low energies.
2. **Cluster conformers** (e.g., `RMSDMatrix` in RDKit or `scipy.cluster.hierarchy`) before expensive single-point calculations; keep one representative per cluster.
3. **Adopt an adaptive sampling loop** (generate-score-prune-repeat) to focus only on the thermally accessible region of phase space—`molSimplify` and `stk` both expose hooks for this.

***

## 7. Bottom line

For coordination complexes, **`molSimplify` + `CREST` (via `pycrest`) + `xTB`** is the quickest full-stack solution that runs entirely in Python, but **`stk`** and **`Molassembler`** are equally capable if you prefer a graph-theoretic or supramolecular-chemistry-oriented API. All three libraries make it straightforward to loop over ligand sets, enumerate stereoisomers, sample conformers, calculate energies, and finally compute ensemble-averaged observables with a few dozen lines of Python. Happy enumerating!
