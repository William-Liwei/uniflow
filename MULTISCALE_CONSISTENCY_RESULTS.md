# Generated Multiscale Consistency Results

This experiment re-encodes real and generated sequences with the trained Stage-1 multiscale tokenizer, then checks whether the two scale branches recover consistent public code ids.

Experiment setup:

- Scale-1: patch size `2`, latent dimension `192`.
- Scale-2: patch size `4`, latent dimension `320`.
- `Pair Token Acc`: token-level agreement between the Scale-1-only recovered id and the Scale-2-only recovered id.
- `Pair Seq Acc`: sequence-level exact agreement between the two recovered id sequences.
- `Shared`: normal Stage-2 generation in the shared public subspace.
- `Scale-1 Only` / `Scale-2 Only`: Stage-2 generation restricted to one latent branch.

## Main Consistency Table

| Dataset | Real Pair Token Acc | Shared Pair Token Acc | Scale-1 Only Pair Token Acc | Scale-2 Only Pair Token Acc | Real Pair Seq Acc | Shared Pair Seq Acc |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| sine | 0.6651 | 0.7659 | 0.4987 | 0.5398 | 0.0905 | 0.2010 |
| etth | 0.7109 | 0.7682 | 0.4674 | 0.4770 | 0.1458 | 0.2305 |
| energy | 0.7778 | 0.7893 | 0.4831 | 0.4657 | 0.2500 | 0.2708 |

## Shared-Mapping Margin

| Dataset | Real Shared Margin | Shared Generated Margin | Scale-1 Only Margin | Scale-2 Only Margin |
| --- | ---: | ---: | ---: | ---: |
| sine | 0.019133 | 0.024315 | 0.016853 | 0.016198 |
| etth | 0.041611 | 0.045057 | 0.030867 | 0.029428 |
| energy | 0.040053 | 0.040968 | 0.023519 | 0.023341 |

## Interpretation

The shared generator preserves the tokenizer's cross-scale semantic relation: after generated samples are re-encoded, the two scale branches recover mutually consistent public ids at rates close to or above the real-data reference. In contrast, restricting Stage-2 generation to only one branch sharply reduces cross-scale agreement, especially on ETTh and Energy.

This supports a stronger claim than ordinary DS/FID tables: MAST-Flow is not merely a larger tokenizer. The shared public token space gives the generator a process-level semantic interface that remains recoverable from multiple temporal views, while single-branch generation breaks that cross-scale agreement.

The fact that the shared generated margin can exceed the real margin should be interpreted cautiously. It does not mean generated samples are more realistic than real data; it indicates that the generated samples remain in a stable region of the learned shared-token manifold. Distributional realism should still be judged together with DS/FID and the nearest-neighbor sanity check.

## Generated Files

- `output/multiscale_consistency/sine/summary.json`
- `output/multiscale_consistency/sine/summary.md`
- `output/multiscale_consistency/etth/summary.json`
- `output/multiscale_consistency/etth/summary.md`
- `output/multiscale_consistency/energy/summary.json`
- `output/multiscale_consistency/energy/summary.md`

