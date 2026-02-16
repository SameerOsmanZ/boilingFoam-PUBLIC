# mukherjee2011

3D flow boiling in a rectangular microchannel reproducing Mukherjee et al. (2011) experiments.  
Solver: `icoBoilingFoam` — fluid-only (no CHT), wall temperature prescribed via `fixedValue` BC.  
Mesh: 2,750,000 cells (550 × 100 × 50). Originally for OpenFOAM v2106, ported to **v2506**.

## Adjustments for v2506

### Allrun scripts (both stages)
The original scripts were incomplete stubs (mesh/copy only, no solver execution). Completed with:
- `decomposePar -region fluid` + `mpirun -np 16` + `reconstructPar` (both stages)
- twoPhase Allrun still handles the `.tp` config swap (controlDict, fvSchemes, fvSolution)

### decomposeParDict (system/fluid/)
- `numberOfSubdomains`: 64 → **16** (local machine)
- Fixed `object` name: `changeDictionaryDict` → `decomposeParDict`

### system/fluid/controlDict
- Fixed application name: `interFoamHardtPhaseChange` → `icoBoilingFoam`
- Fixed `writeCompression`: `uncompressed` → `no` (v2506 syntax)

### SLURM scripts (of_job.sh)
- Updated OpenFOAM module reference: v2106 → v2506

### Note: no `libs (thermoTools)` needed
This case has no CHT coupling — wall temperature is directly set via `fixedValue`. No additional runtime libraries are required.

## Running

```bash
# Stage 1: develop velocity + temperature fields (frozenFlow = false)
cd singlePhase
./Allclean && ./Allrun

# Stage 2: two-phase boiling with seeded bubble
cd ../twoPhase
./Allclean && ./Allrun
```
