# Benchmarking Multimodal Transformers

This repository collects code, data and results used to benchmark several multimodal transformer models for three tasks:

- Retrieval (image-to-text and text-to-image using embedding similarity)
- Captioning (automatic caption generation)
- Zero-shot classification (text-based zero-shot labels or model-based zero-shot predictions)

The primary analysis is in the notebook `Benchmarking Multimodal Transformers for Retrieval, Captioning and Zero-Shot Classification.ipynb`. Supporting data, embeddings and evaluation outputs live in `vlm_benchmark_storage/`.

**Repository layout**
- `Benchmarking Multimodal Transformers for Retrieval, Captioning and Zero-Shot Classification.ipynb` — analysis notebook (main entrypoint).
- `vlm_benchmark_storage/` — storage for inputs and outputs used by the notebook:
  - `captions/` — per-model caption text files (one caption per image, same image order used across files).
  - `embeddings/` — saved numpy arrays of image and caption embeddings (npy/npz). Naming convention: `{model}_img_embeds.npy` and `{model}_cap_embeds.npy`.
  - `results/` — per-model JSON results and `complete_comparison.csv` that aggregates key metrics.
- `README.md`, `.gitignore`, `requirements.txt` — repo metadata and environment configuration.

## Quick start (Windows PowerShell)

1) Create and activate a virtual environment:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

2) Install dependencies from the provided `requirements.txt`:

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

3) Launch Jupyter and open the notebook:

```powershell
jupyter lab
# or
jupyter notebook "Benchmarking Multimodal Transformers for Retrieval, Captioning and Zero-Shot Classification.ipynb"
```

4) Run the notebook cells in order. The notebook expects `vlm_benchmark_storage/` to exist and contain the example files shipped with this repository.

Tip: to run the notebook non-interactively and save outputs, use `nbconvert`:

```powershell
jupyter nbconvert --to notebook --execute "Benchmarking Multimodal Transformers for Retrieval, Captioning and Zero-Shot Classification.ipynb" --output executed.ipynb
```

## Expected embeddings format

- Image embeddings: numpy array with shape `(N, D_img)` saved as `{model}_img_embeds.npy`.
- Caption embeddings/text embeddings: numpy array with shape `(N, D_text)` saved as `{model}_cap_embeds.npy`.
- `N` must match across models (same image ordering) for retrieval evaluation.

Example Python snippet to load embeddings and compute cosine similarity scores:

```python
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

img = np.load('vlm_benchmark_storage/embeddings/clip_img_embeds.npy')  # (N, D)
cap = np.load('vlm_benchmark_storage/embeddings/clip_cap_embeds.npy')  # (N, D)
scores = cosine_similarity(img, cap)  # shape (N, N)
```

## How to add a new model

1. Save captions: `vlm_benchmark_storage/captions/{model}_captions.txt` (one caption per image, same order).
2. Save embeddings: `vlm_benchmark_storage/embeddings/{model}_img_embeds.npy` and `{model}_cap_embeds.npy`.
3. Re-run the notebook cells that compute evaluation metrics. New results will be written to `vlm_benchmark_storage/results/` and the `complete_comparison.csv` file will be updated.

## Interpreting results

- Per-model JSON files (in `vlm_benchmark_storage/results/`) contain detailed metrics and sample predictions.
- `complete_comparison.csv` contains aggregated metrics (e.g., retrieval recall@1/5/10, captioning scores such as BLEU/CIDEr if computed, and zero-shot accuracy).

If a metric is unclear, open the JSON file for samples and the notebook cell that computes it to inspect how it is derived.

## Pushing this repository to GitHub

1. Initialize or confirm git state and add remote (one-time):

```powershell
git init  # if not already a repo
git remote add origin https://github.com/R15n1k/Benchmarking-Multimodal-Transformers-for-Retrieval-Captioning-and-Zero-Shot-Classification.git
```

2. Make sure `.gitignore` excludes large embedding files (recommended):

```text
vlm_benchmark_storage/embeddings/*.npy
.venv/
```

3. Commit and push (recommended flow):

```powershell
git add .
git commit -m "Initial project files: notebook, data layout, README"
git branch -M main
git push -u origin main
```

If `git push` fails because of very large objects (GitHub limit is ~2GB per object), either remove large files from the repo or use Git LFS. See below.

### Handling large files

- Safe (recommended): keep embeddings outside git, upload to cloud storage and document download instructions in `README.md` — this avoids large pushes and keeps repo small.
- Track with Git LFS: to store large binaries in LFS and migrate existing `.npy` objects:

```powershell
git lfs install
git lfs track "*.npy"
git add .gitattributes
git commit -m "Track .npy with Git LFS"
git lfs migrate import --include="vlm_benchmark_storage/embeddings/*.npy"
git push --force --set-upstream origin main
```

Warning: `git lfs migrate` rewrites history and requires a forced push. Only use it if you understand the implications for collaborators.

## Reproducibility & environment

- A `requirements.txt` is provided for pip installs. On GPU machines, install a CUDA-compatible `torch` wheel (see https://pytorch.org/get-started/locally).
- To create a conda environment instead, create an `environment.yml` and install packages accordingly.
