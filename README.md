# WILLIE

**A unified vision-transformer framework and benchmark for wound classification, segmentation, and localization.**

Accepted at the Machine Learning for Healthcare Conference (MLHC) 2026.

> **Note on data:** This repository does **not** redistribute the wound datasets. The images are governed by their original licenses and must be obtained from their original sources (see [Datasets](#datasets)). Only data *manifests and splits* are included, so results can be reproduced against the original images.

---

## Overview

WILLIE is a single framework that performs three wound-analysis tasks — classification, segmentation, and localization — over a shared 5-class taxonomy, together with a benchmark that compares it against task-specific baselines.

Two findings from the paper:

- **Segmentation-derived localization beats dedicated detectors.** Deriving wound location from the segmentation mask outperformed the object-detection baselines on this benchmark.
- **Scaling is task-dependent.** Larger models did not help uniformly across the three tasks; the benefit of scale varied by task (see the scaling analysis notebook and the paper).

The framework is released at three sizes:

| Scale | Parameters |
|-------|-----------|
| MINI  | 34.3M |
| BASE  | 520.4M |
| XL    | 762.5M |

### Reported headline results

As reported in the paper (test set). See the paper for the exact evaluation protocol (test split, test-time augmentation, and model selection), which differs from the cross-validation ablation protocol.

| Task | Metric | Score |
|------|--------|-------|
| Classification | Accuracy | 91.88% |
| Segmentation | Dice | 91.41% |
| Localization | AP@0.5 | 96.23% |

Component ablations (F²DCA, WACSA, MoE, WTCS) are in [`ablations/`](ablations/) and the paper. Reported component effects fall within cross-validation noise; read the ablation section of the paper for the honest interpretation before citing individual components as contributions.

---

## Repository structure

The pipeline is organized as numbered notebooks, intended to be run in order.

| Notebook | Purpose |
|----------|---------|
| `01_WILLIE_DataForge.ipynb` | Data preparation, cleaning, split generation |
| `02_WILLIE_DetectionEngine.ipynb` | Detection baseline pipeline |
| `03_WILLIE_Cls_Baselines.ipynb` | Classification baselines |
| `04_WILLIE_Seg_Baselines.ipynb` | Segmentation baselines |
| `05_WILLIE_SAM2_vs_MedSAM.ipynb` | SAM2 vs MedSAM comparison |
| `06_WILLIE_MINI.ipynb` | MINI model (34.3M) |
| `07_WILLIE_BASE.ipynb` | BASE model (520.4M) |
| `08_WILLIE_XL.ipynb` | XL model (762.5M) |
| `09_WILLIE_Scaling_Analysis.ipynb` | Scaling behavior across tasks |
| `10_WILLIE_Cls_Comparison.ipynb` | Classification comparison |
| `11_WILLIE_Det_Comparison.ipynb` | Detection/localization comparison |
| `12_WILLIE_All_Figures_Tables.ipynb` | Figures and tables |
| `13_WILLIE_Paper_Figures.ipynb` | Paper-ready figures |
| `14_WILLIE_HospitalPipeline.ipynb` | End-to-end deployment-style pipeline |

| Directory | Contents |
|-----------|----------|
| `04_Manifests_and_Splits/` | (verify contents) — the authoritative manifests/splits are under `data/processed/`; confirm before relying on this folder |
| `05_Figures/` | Generated figures |
| `06_Tables/` | Generated tables |
| `08_SAM2_Generated_Masks/` | SAM2-generated masks |
| `09_Detection_Predictions/` | Detection outputs |
| `ablations/` | Ablation configs and results |

> Before publishing: clear notebook outputs (`jupyter nbconvert --clear-output --inplace *.ipynb`). Output cells can embed licensed images and absolute local paths.

---

## Datasets

The benchmark uses three public wound datasets, mapped to a 5-class taxonomy: `diabetic`, `pressure`, `surgical`, `venous`, `no_wound`. The images are governed by their original licenses and are **not** included in this repository — obtain each from its original source.

- **FUSeg** (Foot Ulcer Segmentation Challenge 2021) — `https://fusc.grand-challenge.org` and the UWM `wound-segmentation` repository.
- **AZH** (AZH Wound and Vascular Center dataset) — UWM Big Data Lab / `uwm-bigdata` GitHub repositories.
- **Medetec** (Medetec Wound Database) — `medetec.co.uk`.

After downloading, place the images where the manifests in `manifests/` expect them (see `01_WILLIE_DataForge.ipynb`). The manifests reference images by relative path, so results reproduce without re-hosting the images.

---

## Environment

Dependencies come from the `wound` conda environment. Do not install guessed versions — generate a lock file from that environment (see `environment.txt` for the full procedure):

```bash
conda activate wound
pip freeze > requirements.txt
# record the exact torch + CUDA build:
python -c "import torch; print(torch.__version__, torch.version.cuda)"
```

Install from the generated file:

```bash
conda create -n wound python=<version-from-env>
conda activate wound
pip install -r requirements.txt
```

**TODO (before release):** commit the generated `requirements.txt`, and state the GPU(s) and memory used for XL training/eval so others can gauge hardware requirements.

---

## Reproducing results

1. Set up the `wound` environment.
2. Obtain the three datasets and place them per the manifests.
3. Run `01_WILLIE_DataForge.ipynb` to build/verify splits.
4. Run the model notebooks (`06`–`08`) for MINI/BASE/XL.
5. Run comparison and figure notebooks (`09`–`13`) to regenerate the paper's tables and figures.

The notebook that produces each headline number should reproduce that number under the paper's stated protocol. If a number does not reproduce from its notebook, treat that as a bug to fix before release, not a footnote.
---

## Acknowledgements

Developed in the Qian Group, University of Houston. Advisor: Dr. Peizhu Qian.
