# EMCAD on ClinicDB: Independent Experimental Reproduction

Independent experimental reproduction of **PVT-EMCAD-B2** from the CVPR 2024 EMCAD paper on **ClinicDB** — **95.02% Dice** vs. **95.21% reported**.

Independent experimental reproduction and analysis of:

> **EMCAD: Efficient Multi-scale Convolutional Attention Decoding for Medical Image Segmentation**  
> Md Mostafijur Rahman, Mustafa Munir, and Radu Marculescu  
> *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), 2024*

**Paper:**  
https://openaccess.thecvf.com/content/CVPR2024/html/Rahman_EMCAD_Efficient_Multi-scale_Convolutional_Attention_Decoding_for_Medical_Image_Segmentation_CVPR_2024_paper.html

**Official implementation:**  
https://github.com/SLDGroup/EMCAD

This project reproduces the **PVT-EMCAD-B2** segmentation pipeline on **ClinicDB** using the authors' publicly released EMCAD implementation.

The goal was not simply to execute the repository, but to independently configure the experimental pipeline, train the model in a different computational environment, select the checkpoint using validation performance, perform a standalone test evaluation, and compare the resulting performance with the published benchmark.

---

## Repository Structure

```text
EMCAD-ClinicDB-Reproduction/
│
├── README.md
├── LICENSE
├── requirements.txt
│
├── notebooks/
│   └── emcad_clinicdb_reproduction.ipynb
│
├── results/
│   ├── metrics/
│   │   ├── final_test_metrics.xlsx
│   │   └── training_summary.xlsx
│   │
│   └── qualitative/
│       ├── 01_363.png
│       ├── 02_575.png
│       ├── 03_590.png
│       ├── 04_182.png
│       ├── 05_139.png
│       └── 06_8.png
│
└── docs/
    ├── experimental_execution_summary.pdf
    └── technical_analysis.pdf
```

### Directory Overview

| Path | Description |
|---|---|
| `notebooks/` | Complete experimental notebook for the ClinicDB reproduction |
| `results/metrics/` | Final test metrics and training summary |
| `results/qualitative/` | Representative qualitative segmentation results |
| `docs/` | Experimental execution summary and technical analysis |
| `requirements.txt` | Python dependencies used for the reproduction |
| `LICENSE` | License information for this repository |
| `README.md` | Project overview, methodology, results, and reproducibility notes |

---

## 🔬 Reproduction Result

| Metric / Setting | Published EMCAD | My Reproduction |
|---|---:|---:|
| Model | PVT-EMCAD-B2 | PVT-EMCAD-B2 |
| Dataset | ClinicDB | ClinicDB |
| Input resolution | 352 × 352 | 352 × 352 |
| Training epochs | 200 | 200 |
| Batch size | 16 | 4 |
| GPU | RTX A6000 48 GB | NVIDIA Tesla T4 |
| Runs | 5-run average | 1 run |
| **Dice** | **95.21%** | **95.02%** |

**Difference: 0.19 percentage points**

The reproduced result closely approaches the published ClinicDB benchmark despite differences in hardware, software environment, batch size, and number of runs.

> **Important:** This is an implementation-based reproduction under different computational conditions, not an exact reproduction of the authors' original experimental environment.

---

## 📊 Final Test Performance

The best checkpoint was selected using validation Dice and then reloaded for a separate evaluation over the complete **62-image ClinicDB test split**.

| Metric | Result |
|---|---:|
| **Dice** | **0.950151 (95.02%)** |
| IoU | 0.907202 |
| Sensitivity | 0.954845 |
| Specificity | 0.996946 |
| Precision | 0.948724 |
| HD95 | 8.108 |
| Test images | 62 |

**Best epoch:** 160  
**Best validation Dice:** 0.936713

---

## 🧠 What is EMCAD?

EMCAD is an efficient multi-scale convolutional attention decoder designed for medical image segmentation.

The architecture combines:

- **MSCAM — Multi-Scale Convolutional Attention Module**
  - Channel Attention Block (CAB)
  - Spatial Attention Block (SAB)
  - Multi-Scale Convolution Block (MSCB)

- **LGAG — Large-Kernel Grouped Attention Gate**
  - Efficiently fuses decoder and skip-connection features.

- **EUCB — Efficient Up-Convolution Block**
  - Uses depth-wise convolution for computationally efficient upsampling.

A central design idea is the use of **parallel multi-scale depth-wise convolutions**, allowing the decoder to capture spatial information at multiple receptive-field scales while keeping computational cost low.

For architectural details, please refer to the original EMCAD paper and official implementation linked above.

---

## ⚙️ Experimental Environment

| Component | Configuration |
|---|---|
| Platform | Kaggle |
| GPU | NVIDIA Tesla T4 |
| Framework | PyTorch 2.10.0+cu128 |
| CUDA | Enabled |
| timm | 1.0.26 |
| Encoder | PVTv2-B2 |
| Encoder initialization | ImageNet pretrained |
| Decoder | EMCAD |
| Input resolution | 352 × 352 |
| Batch size | 4 |
| Optimizer | AdamW |
| Weight decay | 1 × 10⁻⁴ |
| Maximum epochs | 200 |
| Gradient clipping | 0.5 |
| Multi-scale kernels | [1, 3, 5] |

The batch size was reduced from 16 to 4 because the reproduction was performed on a Tesla T4 rather than the 48 GB RTX A6000 used in the original experiments.

---

## 📈 Training and Checkpoint Selection

Training was performed for a maximum of **200 epochs**.

The highest validation Dice occurred at:

- **Epoch:** 160
- **Validation Dice:** 0.936713

The epoch-160 checkpoint was therefore selected rather than the final epoch.

The selected checkpoint was subsequently reloaded and evaluated independently on all 62 test images.

This standalone evaluation produced the final reproduction Dice of **0.950151 (95.02%)**.

---

## 🔍 Qualitative Analysis

The reproduction also examined predictions across different levels of segmentation difficulty.

### Challenging Examples

| Image | Dice |
|---|---:|
| 363.png | 0.7188 |
| 575.png | 0.8414 |

These examples show larger boundary and shape disagreement between the prediction and ground-truth segmentation.

### Representative High-Performing Examples

| Image | Dice |
|---|---:|
| 590.png | 0.9430 |
| 182.png | 0.9439 |
| 139.png | 0.9767 |
| 8.png | 0.9787 |

These cases demonstrate close agreement between the EMCAD prediction and the annotated polyp region.

---

## 🖼️ Qualitative Results

### Challenging Case — Dice 0.7188

![Case 363](results/qualitative/01_363.png)

### Challenging Case — Dice 0.8414

![Case 575](results/qualitative/02_575.png)

### Representative Case — Dice 0.9430

![Case 590](results/qualitative/03_590.png)

### Representative Case — Dice 0.9439

![Case 182](results/qualitative/04_182.png)

### High-Agreement Case — Dice 0.9767

![Case 139](results/qualitative/05_139.png)

### High-Agreement Case — Dice 0.9787

![Case 8](results/qualitative/06_8.png)

---

## 🧪 Reproduction Workflow

```text
Official EMCAD implementation
          ↓
Environment configuration
          ↓
PVTv2-B2 + EMCAD initialization
          ↓
ImageNet-pretrained encoder
          ↓
ClinicDB preprocessing
          ↓
200-epoch training
          ↓
Validation-based checkpoint selection
          ↓
Reload best checkpoint
          ↓
Standalone 62-image test evaluation
          ↓
Published vs. reproduced benchmark comparison
          ↓
Quantitative + qualitative error analysis
```

---

## 📁 Reproducibility Artifacts

This repository provides the main artifacts needed to inspect and reproduce the experiment:

- **Notebook:** complete experimental workflow and execution notes.
- **Requirements:** Python dependencies used in the reproduction environment.
- **Final metrics:** standalone test-set evaluation.
- **Training summary:** training and validation history.
- **Qualitative results:** representative segmentation predictions.
- **Experimental execution summary:** concise record of setup, execution, and benchmark verification.
- **Technical analysis:** analysis of EMCAD's design choices, ablations, and reproduction findings.

---

## 🔎 Key Findings

1. **Close benchmark agreement:** the reproduction achieved **95.02% Dice**, compared with the published **95.21%** five-run average.
2. **Small benchmark gap:** the difference is approximately **0.19 percentage points**.
3. **Validation-based selection:** the best checkpoint occurred at epoch **160**, rather than the final training epoch.
4. **Hardware-constrained reproduction:** the experiment was performed on a **Tesla T4** with batch size **4**, compared with the original **RTX A6000 48 GB** and batch size **16**.
5. **Qualitative variability:** performance varied across individual test cases, motivating inspection beyond the aggregate Dice score.

---

## 📚 Original Work and Attribution

This repository uses the publicly released implementation of the original EMCAD work.

**Original authors:**

- Md Mostafijur Rahman
- Mustafa Munir
- Radu Marculescu

**Original paper:**

> Md Mostafijur Rahman, Mustafa Munir, and Radu Marculescu.  
> *EMCAD: Efficient Multi-scale Convolutional Attention Decoding for Medical Image Segmentation.*  
> Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), 2024.

Please cite the original paper and acknowledge the official implementation when using this repository or building upon the EMCAD code.

**Paper:**  
https://openaccess.thecvf.com/content/CVPR2024/html/Rahman_EMCAD_Efficient_Multi-scale_Convolutional_Attention_Decoding_for_Medical_Image_Segmentation_CVPR_2024_paper.html

**Official implementation:**  
https://github.com/SLDGroup/EMCAD

### BibTeX

```bibtex
@InProceedings{Rahman_2024_CVPR,
    author    = {Rahman, Md Mostafijur and Munir, Mustafa and Marculescu, Radu},
    title     = {EMCAD: Efficient Multi-scale Convolutional Attention Decoding for Medical Image Segmentation},
    booktitle = {Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    year      = {2024},
    pages     = {11769--11779}
}
```

---

## 👤 About This Reproduction

**Audity Ghosh**

This repository documents an independent experimental reproduction of EMCAD conducted as part of research preparation in medical image analysis and efficient deep learning.

The focus of this work is **reproducibility, experimental verification, and critical analysis** rather than claiming a new implementation or modification of EMCAD.
