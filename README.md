# Phase Stability of Lead (Pb)

## Overview

This notebook demonstrates a workflow for estimating the pressure- and temperature-dependent phase stability of pure lead (Pb). It compares three crystal structures:

- Face-centred cubic (FCC)
- Hexagonal close-packed (HCP)
- Body-centred cubic (BCC)

At fixed temperature and pressure, the phase with the lowest Gibbs free energy is thermodynamically preferred. The notebook estimates FCC/HCP and HCP/BCC phase boundaries by locating where the corresponding Gibbs free-energy differences change sign.

**This is a workflow demonstration, not a quantitatively accurate phase diagram of lead.** The notebook reports transition pressures near 57 and 84 GPa for the selected EAM potential, while experimental transition pressures are approximately 13 and 109 GPa.

## Method

For each crystal phase, the workflow evaluates the Helmholtz free energy over a volume grid using the quasiharmonic approximation. The free energy includes:

- Static-lattice energy
- Harmonic vibrational free energy from phonons

At each temperature and pressure, the workflow minimises

\[
G(T,P) = \min_V [F(V,T) + PV]
\]

using a polynomial fit in volume. The volume grid is re-centred and narrowed around the minimum until the Gibbs free energy converges. The calculated Gibbs free energies are then compared for FCC/HCP and HCP/BCC pairs. Interpolation of the zero crossings in the free-energy differences provides estimates of the phase-boundary pressures as a function of temperature.

The quasiharmonic approximation accounts for thermal expansion through the volume dependence of phonon frequencies and includes zero-point motion. It does not include anharmonicity at fixed volume or phonon-phonon scattering.

## Workflow outline

1. Define initial FCC, HCP and BCC lead structures using phase-specific volume estimates.
2. Construct reusable `pmd03.gibbs_over_pressures` workflow nodes for the FCC/HCP low-pressure pair and HCP/BCC high-pressure pair.
3. Evaluate Gibbs free energies over pressure and temperature grids.
4. Load the precomputed HDF5 result when available, or run the calculations.
5. Plot the HCP–FCC and BCC–HCP Gibbs free-energy differences.
6. Interpolate the free-energy crossovers and plot the estimated pressure-temperature phase boundaries.

## Repository contents

The workflow requires the notebook and the `pmd03` package from the demonstrator repository. The notebook also refers to the following data files:

```text
resources/Pb_II_Wang_2018.eam.alloy
results/phase_run.h5
```

- `resources/Pb_II_Wang_2018.eam.alloy` is required when executing the calculation rather than loading cached results.
- `results/phase_run.h5` is the cached calculation used by the notebook's default settings (`use_result = True`).

Make sure both files are present at the paths shown above if you expect the notebook to run unchanged. If the cached result is not included, set `use_result = False` to execute the workflow; the EAM potential file must then be present. The notebook's `write_result` option controls whether a new HDF5 cache is saved.

## Software and environment

The environment is based on the repository's `pyproject.toml`. The Conda environment below installs the project and its declared runtime dependencies in editable mode.

```bash
conda env create -f environment.yml
conda activate pmd03-ex3
jupyter lab
```

Open `ex3-phase_stability.ipynb` in JupyterLab.

The notebook imports `ase`, `bagofholding`, `numpy`, `matplotlib`, `pyiron_workflow`, `pyiron_workflow_atomistics`, and the local `pmd03` package. The project `pyproject.toml` should declare and pin the corresponding runtime dependencies; do not maintain a second, divergent dependency list in this README.

## Calculation settings

The notebook provides two modes:

- **Full settings** (`cheap = False`): 12 temperatures from 0 to 600 K; five low-pressure points and seven high-pressure points; Gibbs tolerance of `2e-4`.
- **Cheap settings** (`cheap = True`): three temperatures from 0 to 600 K; two low-pressure points and two high-pressure points; Gibbs tolerance of `2e-3`. This mode is intended for testing, not detailed physical interpretation.

The workflow uses process-based parallel execution. Runtime depends on available CPU cores and may be substantially longer on hosted notebook services.

## Outputs

The notebook produces:

- Gibbs free-energy difference plots for HCP–FCC and BCC–HCP as functions of temperature at selected pressures.
- Estimated FCC/HCP and HCP/BCC transition pressures at each sampled temperature.
- A pressure-temperature plot showing the interpolated phase boundaries.

## Important limitations

- **Interatomic potential:** The predicted transition pressures differ substantially from experiment. Results should be interpreted as a demonstration of the workflow and the selected potential, not as a validated phase diagram.
- **Quasiharmonic approximation:** Anharmonicity at fixed volume and phonon-phonon scattering are not included.
- **Interpolation:** Phase boundaries are inferred from a finite pressure-temperature sampling grid; their accuracy depends on grid density, convergence, and interpolation behaviour.
- **Knowledge graph:** The notebook explicitly skips knowledge-graph generation because the current `semantikon` parser used by the demonstrator cannot parse the workflow's loop/flow-controller structure.

## Attribution

This workflow is based on the `ex3-phase_stability.ipynb` demonstrator in the `pyiron-node-store/pmd03_demonstrators` repository. 
