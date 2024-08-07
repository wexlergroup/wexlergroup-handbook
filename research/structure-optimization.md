# Ionic Minimization

## Steps

### 1. Get the crystal structure

* **Materials Project**: [https://next-gen.materialsproject.org/](https://next-gen.materialsproject.org/)
* **ICSD**: [http://libproxy.wustl.edu/login?url=https://libguides.wustl.edu/passwords](http://libproxy.wustl.edu/login?url=https://libguides.wustl.edu/passwords) (login and scroll down to Inorganic Crystal Structure Database)

### 2. Minimize the total energy by varying the ionic positions, cell volume, and cell shape

{% hint style="info" %}
We use the `MPScanRelaxSet` in `pymatgen` for DFT calculations.

[https://pymatgen.org/pymatgen.io.vasp.html#pymatgen.io.vasp.sets.MPScanRelaxSet](https://pymatgen.org/pymatgen.io.vasp.html#pymatgen.io.vasp.sets.MPScanRelaxSet)

Do not use different VASP input tags or tag values without discussing them with Prof. Wexler.
{% endhint %}

{% hint style="info" %}
Do you understand the _**Pulay stress problem**_?

If not, please read the following page in the VASP Wiki and discuss it with Wexler Group members:
{% endhint %}

{% embed url="https://www.vasp.at/wiki/index.php/Energy_vs_volume_Volume_relaxations_and_Pulay_stress" %}

### 3. Determine the minimum basis for total energy convergence

{% hint style="info" %}
You should be converging the total energy to 1 meV/atom.
{% endhint %}

{% hint style="info" %}
Do you understand the _**projector augmented-wave method**_?

If not, please read the following page and its references in the VASP Wiki and discuss it with Wexler Group members:
{% endhint %}

{% embed url="https://www.vasp.at/wiki/index.php/Projector-augmented-wave_formalism" %}

### 4. Determine the minimum integration grid for total energy convergence

{% hint style="info" %}
You should be converging the total energy to 1 meV/atom.
{% endhint %}

{% hint style="info" %}
Do you understand _**k-point integration**_?

If not, please read the following page and its references in the VASP Wiki and discuss it with Wexler Group members:
{% endhint %}

{% embed url="https://www.vasp.at/wiki/index.php/K-point_integration" %}

### 5. Repeat step 2 using the minimum basis and integration grid
