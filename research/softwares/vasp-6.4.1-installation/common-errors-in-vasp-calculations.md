# Common errors in VASP calculations

#### 1. ZBRENT: fatal error in bracketing

First, try to restart the calculation from the most recent structure of the current calculation by copying CONTCAR to POSCAR and rerunning. &#x20;

If it does not work, reduce the EDIFF in INCAR file and restart the calculation from the most recent structure.&#x20;

#### 2. Orbital orthonormalization failed in the inversion of matrix

Visualize the structure to check if there are any atoms that are too close to each other. If possible, regenerate the structure to ensure the atoms are appropriately spaced. Alternatively, manually adjust the position of the atoms to achieve the expected distance. Then, restart the calculation.

#### 3.  Inconsistent Bravais lattice types found for crystalline and reciprocal lattice

Try loosening the change the SYMPREC parameter in the INCAR file, for instance, 1e-08 to 1e07. &#x20;

\




\
