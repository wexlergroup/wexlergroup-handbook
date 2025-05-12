# Additional Methods and Resources

## Sampling

### "Demon" Algorithm

The demon algorithm is a Monte-Carlo scheme for approximating the **micro-canonical ensemble** by adding a single extra degree of freedom—the _demon_—that can store or supply energy so the **total** energy of _system + demon_ stays fixed, while the system energy itself is allowed to fluctuate slightly. Moves that _lower_ the system’s energy always succeed (the demon absorbs the excess); moves that _raise_ it succeed only if the demon has enough energy to donate; otherwise they are rejected. Because the demon’s energy is constrained to be non-negative—and, in practice, is often given an **upper limit** to keep it from “running off with” the energy—the system explores states near the target energy shell with a much lower rejection rate than a strict fixed-energy Metropolis walk. If the demon’s maximum energy is kept small compared with the system’s total energy, the resulting fluctuations are $$O(1/N)$$ and thermodynamic properties converge to those of the true micro-canonical ensemble. (The average demon energy can even be used to estimate the temperature via $$\langle E_D \rangle \approx k T$$.) For very small systems an explicit upper bound on $$E_D$$​ becomes important; without it the algorithm tends toward uniform sampling of the entire phase-space volume inside the energy surface rather than the constant-energy shell itself.

{% embed url="https://youtu.be/RuzpJMvuM4c?si=IYg76gwqJOHmxSuP" %}

## Enumeration

### Polya's Enumeration Theorem

{% embed url="https://youtu.be/Tkz8QGPHxdA?si=Zdsdav_idMXzlpdC" %}

### Ways To Make Hexagonal Grids

{% embed url="https://www.redblobgames.com/grids/hexagons/" %}

## Books and Notes

1. [A Guide to Monte Carlo Simulations in Statistical Physics by Landau and Binder](https://libproxy.wustl.edu/login?url=https://search.ebscohost.com/login.aspx?direct=true\&db=e000xna\&AN=304835\&site=ehost-live\&scope=site\&ebv=EB\&ppid=pp_Cover)
2. [UCSD Physics 140B : Statistical Physics](https://courses.physics.ucsd.edu/2023/Winter/physics140b/)
3. [Computational Many-Body Physics](https://www.thp.uni-koeln.de/trebst/Lectures/2021-CompManyBody.shtml)
4. [Bayesian probability theory](https://imoox.at/course/bayes22)
