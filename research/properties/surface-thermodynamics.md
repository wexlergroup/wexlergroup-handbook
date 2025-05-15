---
description: Originally Authored by Junchi Chen
---

# Surface Thermodynamics

## Calculating Surface Energies via Slab Models

For a slab embedded in a super cell, its Gibbs free energy is:

$$
G_{slab} = G_{bulk} + 2A \gamma
$$

where $$G_{slab}$$ and $$G_{bulk}$$ are the Gibbs free energy of slab and bulk, respectively. 2$$A$$ is the total surface area of the slab while $$\gamma$$ is the surface energy.

Equivalently,

$$
\gamma = \frac{G_{slab} - G_{bulk}}{2A}  \approx \frac{E_{slab}^{DFT} - G_{bulk}}{2A}
$$

where $$G_{slab}$$ can be reliably replaced by $$E_{slab}^{DFT}$$, especially for the solid-state system in ambient temperature.

Based on its definition, the free energy of the bulk can be expressed as:

$$
G_{bulk} = \sum_{i}^{all} N_{i} \mu_{i}
$$

where $$i$$ goes through _all_ the elements that make up the bulk (e.g., $$i$$ = Mn, S for Mn<sub>_x_</sub>S<sub>_y_</sub>) and $$N_{i}$$ is the number of the $$i$$ atoms in the slab.

Then, the surface energy becomes:

$$
\gamma = \frac{E_{slab}^{DFT} - \sum_{i}^{all} N_{i} \mu_{i}}{2A} = \frac{E_{slab}^{DFT} - \sum_{i}^{all} N_{i} (\Delta\mu_{i} + \mu_{i}^{0})}{2A}
$$

where $$\Delta\mu_{i} = \mu_{i} - \mu_{i}^{0}$$ is the difference between the chemical potential of element $$i$$ in its current state (e.g., Mn<sub>_x_</sub>S<sub>_y_</sub> bulk) and its standard state (e.g., $$\alpha$$-Mn solid and S<sub>8</sub> powder).

Similarly, the standard chemical potential of $$i$$, $$\mu_i^0$$, can be approximately replaced by the DFT energy of its stable element, and so, the surface energy becomes as follow:

$$
\gamma = \frac{E_{slab}^{DFT} - \sum_{i}^{all} N_{i} \Delta\mu_{i} - \sum_{i}^{all} N_{i} E_{i}^{DFT}}{2A}
$$

### Example 1: Surface Energy of a Binary Compound

When considering a binary compound Mn<sub>_x_</sub>S<sub>_y_</sub>,

$$
\gamma = \frac{E_{slab}^{DFT} - N_{Mn} \Delta\mu_{Mn} - N_{S} \Delta\mu_{S} - \sum_{i}^{all} N_{i} E_{i}^{DFT}}{2A}
$$

The formation Gibbs free energy of Mn<sub>_x_</sub>S<sub>_y_</sub> is defined as:

$$
\Delta_{f}G_{Mn_{x}S_{y}} = G_{Mn_{x}S_{y}} - x\cdot G_{Mn} - y\cdot G_{S} = x\cdot \Delta \mu_{Mn} + y\cdot \Delta \mu_{S}
$$

Apparently, the difference in chemical potential of Mn and that of S are related to each other as:

$$
\Delta \mu_{Mn} = \frac{\Delta_{f}G_{Mn_{x}S_{y}} - y\cdot \Delta \mu_{S}}{x}
$$

Therefore,

$$
\begin{align*}
\gamma =& \frac{E_{slab}^{DFT} - N_{Mn} \Delta\mu_{Mn} - N_{S} \Delta\mu_{S} - N_{Mn} E_{Mn}^{DFT} - N_{S} E_{S}^{DFT}}{2A} \nonumber \\
       =& \frac{E_{slab}^{DFT} - N_{Mn} \frac{\Delta_{f}G_{Mn_{x}S_{y}} - y\Delta\mu_{S}}{x} - N_{S} \Delta \mu_{S} - N_{Mn} E_{Mn}^{DFT} - N_{S} E_{S}^{DFT}}{2A} \nonumber \\
       \approx& \frac{E_{slab}^{DFT} - N_{Mn} \frac{\Delta_{f}E^{DFT}_{Mn_{x}S_{y}} - y\Delta\mu_{S}}{x} - N_{S} \Delta \mu_{S} - N_{Mn} E_{Mn}^{DFT} - N_{S} E_{S}^{DFT}}{2A} \nonumber \\
       =& \frac{E_{slab}^{DFT} - \frac{N_{Mn}}{x} E_{Mn_{x}S_{y}}^{DFT} + N_{Mn}E^{DFT}_{Mn} + \frac{y}{x}N_{Mn}E^{DFT}_{S} + (\frac{y}{x}N_{Mn} - N_{S}) \Delta \mu_{S}}{2A} \nonumber \\
       & - \frac{N_{Mn} E_{Mn}^{DFT} + N_{S} E_{S}^{DFT}}{2A} \nonumber \\
       =& \frac{E_{slab}^{DFT} - \frac{N_{Mn}}{x} E_{Mn_{x}S_{y}}^{DFT} + (\frac{y}{x}N_{Mn} - N_{S})E^{DFT}_{S} + (\frac{y}{x}N_{Mn} - N_{S}) \Delta \mu_{S}}{2A} \nonumber \\
       =& \frac{E_{slab}^{DFT} - \frac{N_{Mn}}{x} E_{Mn_{x}S_{y}}^{DFT}}{2A} + \frac{\Gamma_{S}}{2A} E^{DFT}_{S} + \frac{\Gamma_{S}}{2A} \Delta\mu_{S}
\end{align*}
$$

$$
\gamma = \gamma_{0} + \frac{\Gamma_S}{2A} \Delta\mu_S
$$

where

$$
\gamma_{0} = \frac{E_{slab}^{DFT} - \frac{N_{Mn}}{x} E_{Mn_{x}S_{y}}^{DFT}}{2A} + \frac{\Gamma_{S}}{2A} E^{DFT}_{S}
$$

### Example 2: Surface Energy of a Ternary Compound

Under construction...
