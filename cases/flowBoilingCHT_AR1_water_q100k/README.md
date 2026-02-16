# flowBoilingCHT_AR1_water_q100k

3D flow boiling with conjugate heat transfer in a rectangular microchannel (AR=1, water, q=100 kW/m²).  
Solver: `icoBoilingFoam` — originally for OpenFOAM v2106, ported to **v2506**.

## Adjustments for v2506

### controlDict (both singlePhase & twoPhase)
- Added `libs (thermoTools);` — the `compressible::turbulentTemperatureCoupledBaffleMixed` BC moved from `libcompressibleTurbulenceModels` to `libthermoTools` in v2506.

### Allrun scripts (both stages)
The original scripts were incomplete stubs (mesh setup only, no solver execution). Completed with:
- `topoSet -region fluid` + `createPatch` to create the `adiabaticWall` patch (singlePhase)
- `changeDictionary -region fluid/solid` to fix `cellToRegion` BCs (singlePhase)
- `decomposePar -allRegions` + `mpirun` + `reconstructPar` (both stages)
- Fixed `rm -r 0` → `rm -rf 0` to suppress first-run error (singlePhase)

### decomposeParDict (all 4: singlePhase & twoPhase × fluid & solid)
- `numberOfSubdomains`: 256 → **16** (local machine)
- Fixed `object` name: `changeDictionaryDict` → `decomposeParDict`

### orig.0/fluid/cellToRegion (singlePhase)
- Fixed syntax error: dangling `symm` text after a closing brace; added proper `symm { type symmetry; }` entry.

## Running

```bash
# Stage 1: develop temperature field (frozenFlow)
cd singlePhase
./Allclean && ./Allrun

# Stage 2: two-phase boiling (requires completed singlePhase)
cd ../twoPhase
./Allclean && ./Allrun
```
