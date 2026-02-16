# nucleateBoiling_BuresSato

2D axisymmetric (wedge) nucleate pool boiling reproducing Bures & Sato experiments.  
Solver: `icoBoilingFoam` — 3-region CHT (fluid + solid sapphire + thin titanium heater).  
Mesh: ~1.5M base cells (snappyHexMesh refinement near wall). Originally for OpenFOAM v2106, ported to **v2506**.

## Adjustments for v2506

### controlDict (all variants: controlDict, .sp, .tp — both stages)
- Added `libs (thermoTools);` — the `compressible::turbulentTemperatureCoupledBaffleMixed` BC (used for heater↔fluid CHT coupling) moved from `libcompressibleTurbulenceModels` to `libthermoTools` in v2506.

### Allrun scripts (both stages)
The original scripts were incomplete stubs (mesh/copy only, no solver execution). Completed with:
- `decomposePar -allRegions` + `mpirun -np 16` + `reconstructPar` (both stages)
- Fixed `rm -r 0` → `rm -rf 0` to suppress first-run error (singlePhase)
- Replaced hardcoded `0.088` time directory with dynamic latest-time detection (twoPhase)
- Added error check if no converged singlePhase time is found (twoPhase)

### Allclean scripts (both stages)
- Added `rm -rf processor*` to clean up parallel run directories

### heater/fvOptions (all variants: fvOptions, .sp, .tp — both stages)
- Changed `codeAddSup` → `codeAddSupRho` in the `scalarCodedSource` for the heater heat source. In v2506, the solid thermo solver calls the compressible `addSup(rho, eqn, fieldi)` which maps to `codeAddSupRho`; the old `codeAddSup` (incompressible form) triggers a "Not implemented" fatal error.

### decomposeParDict (all 6: 3 regions × 2 stages)
- `numberOfSubdomains`: 256 → **16** (local machine)
- Fixed `object` name: `changeDictionaryDict` → `decomposeParDict`

### SLURM scripts (of_job.sh)
- Updated OpenFOAM module reference: v2106 → v2506

### Note: deprecation warning
The `compressible::turbulentTemperatureCoupledBaffleMixed` BC emits a warning suggesting migration to `compressible::turbulentTemperatureRadCoupledMixed`. This is **non-fatal** and the simulation runs correctly.

## Running

```bash
# Stage 1: develop temperature field (frozenFlow = true)
cd singlePhase
./Allclean && ./Allrun

# Stage 2: two-phase boiling with seeded bubble
cd ../twoPhase
./Allclean && ./Allrun
```
