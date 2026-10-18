# uniflow

## Stage 1

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

## Stage 2

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
