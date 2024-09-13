# Properties

### Calculating formation enthalpy of defect using density functional theory (DFT)

#### 1. Obtain the correct structure &#x20;

Get the unit cell of the system/material from database. Prepare the supercell if needed. You can use [pymatgen](https://pymatgen.org) or [ASE](https://wiki.fysik.dtu.dk/ase/) for preparing supercells and export the structure file in a POSCAR.&#x20;

#### 2. Prepare VASP input files for cell/geometry relaxation

You can use pymatgen for prepare VASP input files i.e. INCAR, POSCAR, KPOINTS, POTCAR. In the INCAR file, choose the following parameters carefully: ISIF, EDIFF, EDIFFG, ISPIN, LDAU (for Hubbard U correction, if needed), MAGMOM (initial guess of magnetic moment of all atoms) and functional. By default, the functional is read from the pseudopotential file, POTCAR.&#x20;

Please ensure that everything is correct in the input files. Once input files are ready, you can submit the job.

1. Relaxation step: First, relax the geometry of the structure to ensure that the atomic positions are at their lowest energy configuration.
2. Total Energy Calculation: Once the structure is relaxed, calculate the total energy of the system. This is the starting point for calculating enthalpy.

#### 3. Calculating the Enthalpy of Formation

Prepare the structure with defect (POSCAR) and repeat the step 2.&#x20;

The enthalpy of formation of defect, e.g., one neutral oxygen vacancy formation energy, can be calculated as follows:&#x20;

&#x20;                         $$\Delta H_{\text{vacancy}} = E_{\text{defective}} - E_{\text{pristine}} - \frac{E_{O_2}}{2}$$

* $$E_{\text{defective}}$$ ​ is the total energy of the system with an oxygen vacancy.
* $$E_{\text{pristine}}$$  is the total energy of the pristine system.
* $$E_{O_2}$$​​ is the energy of a free oxygen molecule&#x20;

Note that DFT typically gives results at 0 K, but real systems operate at higher temperatures.





