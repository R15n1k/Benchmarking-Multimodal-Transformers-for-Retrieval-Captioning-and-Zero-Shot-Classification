# Benchmarking Multimodal Transformers

This repository contains a Jupyter notebook and supporting files used to benchmark several multimodal transformer models for image-caption retrieval, captioning, and zero-shot image classification. The analysis, embeddings, captions, and results are stored under `vlm_benchmark_storage/`.

## Contents

- `Benchmarking Multimodal Transformers for Retrieval, Captioning and Zero-Shot Classification.ipynb` — main notebook used for running experiments and generating results.
- `vlm_benchmark_storage/` — storage for captions, embeddings and results
  - `captions/` — text caption outputs from captioning models (e.g., `blip_captions.txt`, `blip2_captions.txt`).
  - `embeddings/` — numpy arrays of image and caption embeddings (e.g., `blip_img_embeds.npy`, `clip_cap_embeds.npy`).
  - `results/` — per-model JSON results and an aggregated `complete_comparison.csv`.

## Purpose

The project compares modern multimodal transformer pipelines across three tasks:
- Retrieval (image-to-text and text-to-image using embeddings)
- Captioning (automatically generating captions for images)
- Zero-shot classification (using image- or text-based zero-shot approaches)

The notebook implements the data loading, embedding computation, similarity evaluation, and result aggregation. It produces the files under `vlm_benchmark_storage/results/` for later analysis.

## Quick Start (Windows PowerShell)

1. Create and activate a Python virtual environment (recommended):

```powershell
python -m venv .venv; 
.\.venv\Scripts\Activate.ps1
```

2. Install required packages (example list):

```powershell
pip install --upgrade pip
pip install notebook jupyterlab numpy pandas matplotlib scikit-learn pillow torch torchvision transformers sentence-transformers
```

3. Start Jupyter and open the notebook:

```powershell
jupyter lab
# or
jupyter notebook "Benchmarking Multimodal Transformers for Retrieval, Captioning and Zero-Shot Classification.ipynb"
```

4. Run notebook cells sequentially. The notebook expects the `vlm_benchmark_storage/` folder to be present with the example files.

## Reproducing Results

- If you want to regenerate embeddings or captions for a new model: add caption outputs to `vlm_benchmark_storage/captions/` and embeddings (image and caption) to `vlm_benchmark_storage/embeddings/` following the naming pattern used in the repo. Then re-run the notebook cells that compute evaluations — results will be saved to `vlm_benchmark_storage/results/`.
- The aggregated metrics are written to `vlm_benchmark_storage/results/complete_comparison.csv` and per-model JSON files (e.g., `blip_results.json`).

## Adding a New Model

1. Generate captions and save them to `vlm_benchmark_storage/captions/{model}_captions.txt` (one caption per image, same order as the image list used in the notebook).
2. Compute image embeddings and caption embeddings and save as numpy `.npy` files to `vlm_benchmark_storage/embeddings/` using the naming convention `{model}_img_embeds.npy` and `{model}_cap_embeds.npy`.
3. Re-run evaluation cells in the notebook. A new JSON result will be created in `vlm_benchmark_storage/results/` and the CSV will be updated.

## Interpreting Results

- Per-model JSON files contain the evaluation metrics and sample outputs used for debugging.
- `complete_comparison.csv` aggregates key metrics across models for quick comparison (e.g., retrieval recall@k, captioning CIDEr/BLEU/etc., and zero-shot accuracy).

## Uploading to GitHub

If this folder is a git repository already, use these commands (PowerShell):

```powershell
git add README.md "Benchmarking Multimodal Transformers for Retrieval, Captioning and Zero-Shot Classification.ipynb" vlm_benchmark_storage/
git commit -m "Add README and benchmark notebook/results"
git push origin main
```

If you do not have a remote yet, create a GitHub repo and follow the instructions to add `origin` and push.

## Notes and Tips

- Large embedding files can be sizable — consider using Git LFS for any large binary arrays you want tracked in the repo.
- When running on GPUs, ensure `torch` is installed with CUDA matching your drivers; otherwise the CPU-only runtime will still work but will be slower.
- If you want, I can prepare a `requirements.txt` or `environment.yml` for conda, or help set up Git LFS and a `.gitattributes` file.

## Contact

If you want me to push these changes to a remote, or to add a `requirements.txt`, say so and I will prepare the next edits.

---
Generated on: 2025-11-25
