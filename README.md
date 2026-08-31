# Two-Stage Pre-Execution Malware Detection Demo

Reproduces the **two-stage pre-execution detection** architecture (cheap screening →
precise analysis) from the Kaspersky whitepaper *Machine Learning Methods for Malware
Detection*:

- **Stage 1 (Pre-detect)**: lightweight features + supervised similarity hashing
  (random-forest leaf-path hashing) into hash buckets; pure buckets issue verdicts directly
- **Stage 2 (Detect)**: heavy features are extracted only for hard-region files and
  scored by XGBoost (GPU auto-detected, `device='cuda'`)
- Includes a **TLSH** similarity-hashing concept demo (nearest same-family variant vs
  random benign distance distributions) and an error case analysis (per-sample SHAP-style
  attribution for one FP and one FN)

Main file: [`two_stage_pre_execution_detection.ipynb`](two_stage_pre_execution_detection.ipynb)

## Environment

```bash
python3 -m venv .venv
.venv/bin/pip install numpy pandas scikit-learn xgboost matplotlib jupyter ipykernel py-tlsh tqdm
# Jupyter kernel registered as: "Python (malware-demo)"
```

## Data

[EMBER 2018](https://github.com/elastic/ember) — pre-extracted static features of 1.1M
PE files (no raw binaries, safe to work with):

```bash
mkdir -p data && cd data
curl -LO https://ember.elastic.co/ember_dataset_2018_2.tar.bz2   # 1.6 GB
tar -xjf ember_dataset_2018_2.tar.bz2                            # extracts ember2018/*.jsonl
```

The notebook only uses `train_features_{1,3,5}.jsonl` and `test_features.jsonl`,
sampled via random seeks (160K train + 20K test by default) and cached under
`data/cache/` after the first run.

## Run

```bash
.venv/bin/jupyter lab two_stage_pre_execution_detection.ipynb
# or execute headlessly:
.venv/bin/jupyter nbconvert --to notebook --execute --inplace two_stage_pre_execution_detection.ipynb
```

Adjust `N_TRAIN_PER_CLASS` / `N_TEST_PER_CLASS` in the config cell to change the data
scale; delete `data/cache/` to resample.
