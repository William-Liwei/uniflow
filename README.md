# MAST-Flow

Code for **MAST-Flow: Multiscale Aligned Semantic Token Flow Matching for Time Series Generation**.

MAST-Flow extends the single-view SDFlow pipeline with a multiscale Stage-1 tokenizer. Multiple temporal branches share one public code id while keeping branch-specific private embeddings, and Stage 2 performs non-autoregressive categorical flow matching in the resulting shared token space.

## Repository Structure

```text
stage1_vq/          Stage-1 VQ tokenizer training and evaluation
stage2_flow/        Stage-2 flow matching training and generation evaluation
models/             VQ-VAE, multiscale quantizer, and SDFlow/MAST-Flow modules
metrics/            DS, PS, Context-FID, and related evaluation utilities
options/            Command-line option definitions
scripts/            Example Windows/Linux training scripts
utils/              Logging and evaluation helpers
dataset/            Dataset loader code and local data placeholders
```

Large artifacts such as checkpoints, generated arrays, local datasets, paper build folders, and third-party source snapshots are intentionally ignored by `.gitignore`.

## Environment

```bash
conda env create -f environment.yaml
conda activate sdflow
```

If your environment name differs from `sdflow`, activate the environment created from `environment.yaml`.

## Data

Put local benchmark files under `dataset/`. The code supports the dataset names used in the paper, including:

- `sine`
- `stock`
- `etth`
- `energy`

The public GitHub version should normally include loaders and scripts, not large raw data files or generated `.npy` arrays.

## Stage 1: Multiscale Tokenizer

Example Energy configuration with two patch scales:

```bash
python stage1_vq/train_vq.py \
  --batch-size 128 \
  --width 512 \
  --lr 1e-4 \
  --total-iter 100000 \
  --lr-scheduler 200000 \
  --code-dim 512 \
  --nb-code 512 \
  --down-t 2 \
  --depth 3 \
  --dilation-growth-rate 3 \
  --out-dir ./output/output_energy_multipatch_weighted \
  --dataname energy \
  --vq-act relu \
  --quantizer ema_reset_sim \
  --exp-name VQVAE_MS_P24_D192_320_W4060 \
  --window-size 24 \
  --commit 0.001 \
  --multi-scale-vq \
  --multi-scale-mode patch \
  --multi-scale-dims 192 320 \
  --multi-scale-depths 2 4 \
  --multi-scale-dilations 1 3 \
  --multi-scale-patches 2 4 \
  --multi-scale-patch-downs 1 0 \
  --multi-scale-weights 0.4 0.6 \
  --gpu 0
```

Convenience scripts are provided under `scripts/stage1_VQ/`.

## Stage 2: Shared-Token Flow Generation

Train Stage 2 with the matched Stage-1 checkpoint:

```bash
python stage2_flow/train_sdflow.py \
  --vqvae_ckpt ./output/output_energy_multipatch_weighted/VQVAE_MS_P24_D192_320_W4060/net_best_ds.pth \
  --dataname energy \
  --output_dir ./checkpoints_energy_260412 \
  --window_size 24 \
  --down_t 2 \
  --quantizer ema_reset_sim \
  --rank 256 \
  --d_model 512 \
  --n_layers 1 \
  --num_heads 16 \
  --dropout 0.1 \
  --noise_std 0.01 \
  --lambda_mean 0.1 \
  --lambda_std 10.0 \
  --batch_size 64 \
  --lr 1e-4 \
  --lr_uv 1e-3 \
  --max_iters 100000 \
  --print_interval 200 \
  --eval_interval 5000 \
  --eval_steps 20 \
  --device cuda
```

Stage-2 scripts for the main datasets and sequence lengths are under `scripts/stage2_Flow/`.

## Evaluation Utilities

Stage-1 single-scale public-id recovery:

```bash
python stage1_vq/eval_single_scale_latent.py \
  --vqvae_ckpt ./output/output_energy_multipatch_weighted/VQVAE_MS_P24_D192_320_W4060/net_best_ds.pth \
  --output_dir ./output/eval_stage1_energy \
  --dataset_type train \
  --metrics basic
```

Stage-2 generation with shared subspace and single-scale restricted subspaces:

```bash
python stage2_flow/eval_single_scale_generation.py \
  --sdflow_ckpt ./checkpoints_energy_260412/best_ds.pth \
  --vqvae_ckpt ./output/output_energy_multipatch_weighted/VQVAE_MS_P24_D192_320_W4060/net_best_ds.pth \
  --output_dir ./output/eval_stage2_energy \
  --metrics all \
  --store_arrays
```

Generated multiscale consistency check:

```bash
python stage1_vq/eval_generated_multiscale_consistency.py \
  --vqvae_ckpt ./output/output_energy_multipatch_weighted/VQVAE_MS_P24_D192_320_W4060/net_best_ds.pth \
  --array_dir ./output/eval_stage2_energy \
  --output_dir ./output/multiscale_consistency/energy
```

## GitHub Release Notes

Before pushing a public release:

1. Do not commit checkpoints, generated arrays, local raw datasets, paper build artifacts, or copied third-party repositories.
2. Keep scripts, model code, metrics, and reproducibility documentation.
3. If pretrained weights or data are needed, publish them through an external storage service and link them from the README.
4. If the release is anonymous for review, do not include author-identifying paper files.

The repository already has a GitHub remote:

```bash
git remote -v
```

Current remote:

```text
https://github.com/William-Liwei/SDFlow.git
```
