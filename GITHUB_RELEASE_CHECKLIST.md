# GitHub Release Checklist

Use this checklist before pushing a public MAST-Flow code release.

## Keep In Git

- Core code: `stage1_vq/`, `stage2_flow/`, `models/`, `metrics/`, `options/`, `utils/`.
- Example scripts: `scripts/stage1_VQ/`, `scripts/stage2_Flow/`.
- Environment file: `environment.yaml`.
- Documentation: `README.md`, experiment summary markdown files that do not expose private paths or large artifacts.
- Small loader/source files under `dataset/`.

## Keep Local Or External

- Checkpoints: `*.pth`, `*.pt`, `*.ckpt`, `checkpoints*/`.
- Generated arrays/results: `output/`, `*.npy`, `*.npz`, `*.mat`.
- Raw benchmark files: `dataset/*.csv`, `dataset/*.npy`, `dataset/*.mat`.
- Paper source/build folders: `AAAI_AuthorKit27/`, `tex/`, generated PDFs/logs.
- Third-party source snapshots: `TokenFlow/`.
- Python caches: `__pycache__/`, `*.pyc`.

## Before Commit

```bash
git status --short
git diff --stat
git ls-files | rg "__pycache__|\\.pyc$|\\.pth$|\\.npy$|\\.npz$|\\.mat$"
```

The final command should not show cache/checkpoint/generated files unless they are intentionally retained from the old repository history.

## Suggested Commit Scope

For the MAST-Flow release, commit:

- `.gitignore`
- `README.md`
- `GITHUB_RELEASE_CHECKLIST.md`
- Modified core implementation files
- New evaluation scripts
- New training scripts
- Compact result markdown files, if desired

Do not commit the full paper kit or local experimental artifacts unless the release is meant to include paper source.
