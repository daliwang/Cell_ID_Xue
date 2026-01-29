# CellID_Xue

Training and evaluation code for a sparse, stage-aware "Twin Attention" model
that identifies cell identities from partial 3D embryo observations. This repo
is cloned from `git@github.com:HenryX417/Cell_ID.git` and prepared as an
accompanying repository for paper submission.

This folder includes:
- `Model_Code.py`: training pipeline for Sparse Twin Attention v2.4.
- `thefinalevaluation.py`: evaluation pipeline with manifold + KNN voting.

## Quick start

### 1) Create a Python environment and install dependencies

```bash
python -m venv .venv
source .venv/bin/activate
pip install torch numpy scipy scikit-learn matplotlib tqdm
```

If you plan to use GPU, install a CUDA-enabled PyTorch build that matches your
system.

### 2) Prepare data files

Training expects a pickle file named `data_dict.pkl` in the working directory.
Evaluation expects:
- `data_dict.pkl` (training data)
- `evaluation_data_dict.pkl` (held-out eval data)

These files are not included in this repo.

### 3) Train the model

```bash
python Model_Code.py
```

Artifacts are written to the `checkpoints_v2_4/` directory by default.

### 4) Run evaluation

```bash
python thefinalevaluation.py \
  --model_code Model_Code.py \
  --checkpoint checkpoints_v2_4/best_model.pth \
  --train_data data_dict.pkl \
  --eval_data evaluation_data_dict.pkl \
  --output_dir evaluation_final
```

## What the model does

The model encodes sparse 3D cell subsets with:
- Relative geometry and pairwise relational features.
- A total-cell-count embedding to inject developmental stage context.
- A Transformer encoder with a no-match token for open-set alignment.

Training uses pairwise matching between reference and query subsets, optional
temporal consistency, and curriculum scheduling.

Evaluation builds a large embedding manifold and predicts identities with
stage-aware KNN voting and biological error analysis.

## Configuration

Key parameters live in:
- `Config` in `Model_Code.py` (training).
- `EvalConfig` in `thefinalevaluation.py` (evaluation).

Edit those dataclasses to change hyperparameters, sampling strategy, or output
locations.

## Notes

- Paths in `thefinalevaluation.py` default to `v2.4.py`; pass `--model_code`
  explicitly to use `Model_Code.py`.
- The code is designed for C. elegans embryo cell identity tasks, but can be
  adapted to similar 3D point-set matching problems.
