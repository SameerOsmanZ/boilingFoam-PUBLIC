# boilingFoam-PUBLIC
OpenFOAM-based solvers, libraries, and test cases for simulating boiling flows with VOF using both interFoam and isoAdvector. 

Compile with OpenFOAM v2106 or OpenFOAM v2006.  If using a pre-compiled version of OpenFOAM v2106, use pre-compiled patch [211215](https://develop.openfoam.com/Development/openfoam/-/tree/OpenFOAM-v2106.211215/META-INFO?ref_type=tags). <br>

To install OpenFOAM, please follow the intructions [here](https://develop.openfoam.com/Development/openfoam/-/wikis/precompiled/debian).

## OpenFOAM v2506 Port

The `v2506` branch contains all cases and solver ported to **OpenFOAM v2506**. The following changes were applied across all case directories:

### API / Library Changes
- **`libs (thermoTools)`** added to `controlDict` files — the `compressible::turbulentTemperatureCoupledBaffleMixed` BC moved from `libcompressibleTurbulenceModels` to `libthermoTools` in v2506
- **`codeAddSup` → `codeAddSupRho`** in heater `fvOptions` — v2506 solid thermo solver calls the compressible `addSup(rho, eqn, fieldi)` overload
- **`writeCompression`**: `off`/`uncompressed` → `no` (v2506 syntax)

### Case Fixes
- **Allrun scripts**: Completed incomplete stubs with full workflow (`decomposePar` → `mpirun` → `reconstructPar`)
- **Allclean scripts**: Added `rm -rf processor*` for parallel directory cleanup
- **decomposeParDict**: Fixed `object` names (`changeDictionaryDict` → `decomposeParDict`), reduced `numberOfSubdomains` from 256 to 16 for local execution
- **SLURM scripts**: Updated module references from v2106 to v2506

### Cases Ported
| Case | Type | Regions | Status |
|------|------|---------|--------|
| `stefanProblem` | 1D benchmark | fluid | ✅ Minor fixes |
| `suckingInterface` | 1D benchmark | fluid | ✅ Minor fixes |
| `flowBoilingCHT_AR1_water_q100k` | Flow boiling CHT | fluid + solid | ✅ Full repair |
| `mukherjee2011` | Flow boiling | fluid (+ solid/heater) | ✅ Full repair |
| `nucleateBoiling_BuresSato` | Pool boiling CHT | fluid + solid + heater | ✅ Full repair |

See the `README.md` in each case directory for detailed per-case changes.

Documentation
-------------
A detailed documentation with description of the models implemented in __boilingFoam__ and a guide to the tutorials available in this repository can be found [HERE](/doc/boilingFoam.pdf).

# references

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.7991049.svg)](https://doi.org/10.5281/zenodo.7991049)

* F. Municchi, I. El Mellas, O.K. Matar, M. Magnini,
Conjugate heat transfer effects on flow boiling in microchannels,
International Journal of Heat and Mass Transfer,
Volume 195,
2022,
123166,
ISSN 0017-9310,
https://doi.org/10.1016/j.ijheatmasstransfer.2022.123166.

* F. Municchi, C. N. Markides, O.K. Matar, M. Magnini,
Computational study of bubble, thin-film dynamics and heat transfer during flow boiling in non-circular microchannels,
Applied Thermal Engineering,
Volume 238,
2024,
122039,
ISSN 1359-4311,
https://doi.org/10.1016/j.applthermaleng.2023.122039.

