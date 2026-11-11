# Target-Value RBF Global Optimization

Companion Python code for **Target Depth and Power-Function Exploration in RBF Global Optimization** by **Vu Khac Ky** (manuscript JOGO-D-26-00545).

The code compares fixed and cyclic target schedules in global optimization with radial basis function (RBF) surrogates. The studies use Matérn and Gaussian kernels to examine power-function exploration, coverage of the search domain, and performance under a limited evaluation budget. The expanded benchmark includes Sobol sampling and Gaussian-process expected improvement as baselines.

## Installation

Run the commands from the directory containing `requirements.txt`, `jogo/`, and `configs/`. If you are starting from the companion archive `ESM_1.zip`, extract it and enter `JOGO_Reproducibility/` first.

The experiments were run with Python 3.12.14. The package versions are pinned in `requirements.txt`.

```sh
python3.12 -m venv .venv
. .venv/bin/activate
python -m pip install -r requirements.txt
export OPENBLAS_NUM_THREADS=1 OMP_NUM_THREADS=1
python -m unittest discover -s tests -v
```

## Run an example

To check the installation, run the first trajectory in the target-depth study. Write it to a new output directory:

```sh
python -m jogo.runner --config configs/mechanism_full.json \
  --out results/mechanism_example --workers 1 --limit 1
```

This is a quick check, not the full study. Remove `--limit 1` and use a fresh output directory to run the full configuration. Adjust `--workers` to suit your machine.

## Reproduce the reported results

The complete numerical archive is supplied with the revision as **Online Resource 1** (`ESM_1.zip`). It contains the configurations, random seeds, transformation matrices, recorded runs, and analysis scripts. If your checkout does not contain the recorded runs, work from an extracted copy of that archive for the steps below.

| Study | Configuration | Recorded results |
| --- | --- | --- |
| Target-depth comparison with Matérn kernels | `configs/mechanism_full.json` | `results/mechanism_full/` |
| Gaussian reference | `configs/gaussian_reference_full.json` | `results/gaussian_reference_full/` |
| Expanded benchmark | `configs/benchmark_full.json` | `results/benchmark_full/` |
| Kernel geometry comparison | `configs/geometry_full.json` | `results/geometry_revision30/` |
| Candidate pool sensitivity | `configs/geometry_sensitivity_revision.json` | `results/geometry_fixed12/` |
| Extended Ackley budgets | `configs/ackley_extended.json` | `results/ackley_extended/` |

The static prediction comparison is recorded in `results/static_1200/`. The `original/` directory holds earlier runs used for historical comparisons. The archive includes per-run JSON and NPZ files with evaluated points, objective values, best observed values, and diagnostics, as well as a SHA-256 manifest (`FILE_HASHES.json`). Where runs are shared across studies or a shorter budget is a prefix of a longer run, the records do not represent additional independent replications.

### Rebuild tables and figures from the recorded runs

The commands below analyze the stored data without running new optimization trajectories. Run them in a working copy if you want to keep the supplied summaries unchanged.

```sh
python validate_results.py \
  results/mechanism_full results/gaussian_reference_full \
  results/benchmark_full results/geometry_revision30 \
  results/geometry_fixed12 results/ackley_extended
python -m jogo.audit_legacy --original original --out results/legacy_audit
python finish_delivery.py
python analyze_addenda.py
python summarize_revision.py
python revision_figures.py
python export_manuscript_tables.py
python export_addenda_tables.py
```

The tables are exported to `results/manuscript_tables/` and the figures to `results/revision_figures/`. See `results/revision_tables/` for selection, acquisition, and numerical stability checks. The finite candidate searches provide diagnostics; they do not certify the error in minimizing the acquisition function over the continuous domain.

### Run the studies from scratch

Use `python -m jogo.runner` with the configurations above. In a fresh working copy, use the output directory names in the table if you plan to run the summary scripts. To rerun the static comparison, use:

```sh
python -m jogo.static_diagnostic --out results/static_1200 --runs 6 --sizes 1200
```

Use fresh output directories when changing a configuration or the numerical code.

## Citation

If you use this code, please cite:

> Vu Khac Ky. *Target Depth and Power-Function Exploration in RBF Global Optimization*. Manuscript JOGO-D-26-00545.
