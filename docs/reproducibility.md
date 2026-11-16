# Reproducibility Guide

This file contains exact steps, seeds, and environment details to reproduce the published results.

Environment
- Use the provided `environment.yml` (Conda) or `requirements.txt` (pip).
- Recommended Python: 3.9–3.10

Random seeds
- Global seed: 42
- Torch deterministic flags: `torch.manual_seed(42); torch.use_deterministic_algorithms(True)` where applicable

Hardware
- Experiments were run on machines with at least 32GB RAM and an NVIDIA GPU (A100/RTX3080 recommended). CPU-only runs are supported but slower.

Data
- Place raw HIVDB files in `data/raw/` as described in `README.md`.
- Process data with `notebooks/01_data_acquisition.ipynb` or `python src/data_processing.py`.

Standard runs

Sanity test (quick):

```bash
python scripts/run_improved_pipeline.py --subset_size 10 --no_optuna
```

Full reproduce (recommended):

```bash
python scripts/run_improved_pipeline.py --full_data --optuna_trials 50 --results_dir results
```

The full reproduce command performs nested cross-validation and Optuna tuning. Ask for approval before starting it on a shared or costly GPU/CPU allocation. Notebook 07 leaves this capstone disabled by default; set `RUN_IMPROVED_PIPELINE = True` in `notebooks/pipeline_config.py` only when the run is approved.

## Existing artifacts and notebook runs

- Check saved arrays and result tables first. Notebook 01 keeps non-empty processed files, Notebook 02 keeps existing statistics and rare-mutation files, and Notebook 03 validates and reuses pooled/per-residue embeddings. Notebook 03 writes arrays atomically and can rebuild a damaged subset from its valid full-cohort array.
- When retaining execution outputs, write executed notebooks under the existing `notebook_outputs/<notebook-name>/` directories. Keep figures, models, and tables in their established `data/`, `figures/`, and `results/` subdirectories.
- The local source notebooks are templates and may have no saved cell outputs. Do not interpret an existing output file as proof that every notebook cell was freshly executed.
- `python scripts/e2e_execution_verify.py` validates the available files and writes `results/e2e_verification.json`.

Outputs
- Primary results: `results/improved_pipeline/drug_wise_performance.csv`
- Figures: `results/improved_pipeline/figures/`

Notes
- To reproduce exact floating-point results, run on the same hardware and use provided seeds. Some nondeterminism may remain due to parallelism in XGBoost and PyTorch backends.
