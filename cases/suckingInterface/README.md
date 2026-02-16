# suckingInterface

1D sucking interface — phase-change benchmark with condensation-driven interface motion.  
Solver: `icoBoilingFoam` — fluid-only (no CHT), wall BC is `zeroGradient`.  
Mesh: **200 cells** (1D). Runs serially. Originally for OpenFOAM v2106, ported to **v2506**.

## Adjustments for v2506

### controlDict
- Fixed `writeCompression`: `off` → `no` (v2506 syntax)

### Allclean
- Fixed `rm -r AlphaInit/` → `rm -rf AlphaInit/` to suppress error if directory doesn't exist

### Note
- No `libs (thermoTools)` needed — no CHT boundary conditions
- Allrun was already complete (serial run, no decomposition needed)
- decomposeParDict already had correct `object` name

## Running

```bash
./Allclean && ./Allrun
```

Post-processing benchmark plot is generated automatically via `python python/plotBenchmark.py`.
