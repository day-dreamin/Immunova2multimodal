# 📘 Immunova Multimodal AI Pipeline — Reproducibility Instructions

This document explains how to run the full Immunova pipeline, including data preparation, model training, validation, and prediction.  
It assumes access to both **WSI (`.pt`) files** and **omics data**.

---

## 📁 Project Directory Structure

<details>
<summary><strong>Click to expand</strong></summary>

```
Immunova/
├── model.py                    # Defines the multimodal transformer model
├── dataset.py                  # PatientDataset for dataloader
├── train.py                    # Training loop
├── validate.py                 # Evaluation loop
├── predict.py                  # Prediction and visualization
├── Omics_feature.py            # Generate omics_dict.pkl
├── label_feature.py            # Generate label_dict.pkl
├── WSI_feature.py              # Extract WSI features from .pt
├── til_model.pth               # (Optional) Pretrained model

├── wsi_feature/
│   └── wsi_feature.pkl         # Pre-extracted WSI features (from .pt)

├── omics_feature/
│   └── omics_dict.pkl          # Preprocessed omics data per patient

├── label_feature/
│   └── label_dict_ACC.pkl      # Labels (TIL, response, survival)

├── val_ids.txt                 # Patient IDs for validation
├── predictions_response.csv    # Output predictions (generated)

├── Immunova2_Module1/          # WSI patch-level .pt files
│   ├── acc/
│   │   ├── TCGA-XX-YYYY.pt
│   │   ├── TCGA-XX-ZZZZ.pt
│   ├── brca/
│   ├── blca/
│   └── ...                     # Other cancer types

└── Immunova2_module2/          # Omics and clinical data per cancer type
    ├── ACC/
    │   ├── TCGA_clinical_ACC.csv
    │   ├── TCGA_rnaseq_ACC_immune_markers_with_metadata.csv
    │   ├── TCGA_methylation_ACC_immune_sites_with_metadata.csv
    │   ├── TCGA_mirna_ACC_immune_markers_with_metadata.csv
    │   ├── TCGA_rppa_ACC_immune_proteins_with_metadata.csv
    ├── BRCA/
    ├── BLCA/
    └── ...                     # Other cancer types
```

</details>

---

## ⚙️ Code Structure

```
flowchart TD
  A[Raw Data Files<br/>(WSI .pt, Omics CSVs)] --> B[WSI_feature.py<br/>→ wsi_feature.pkl]
  A --> C[Omics_feature.py<br/>→ omics_dict.pkl]
  B --> D[label_feature.py<br/>→ label_dict.pkl]
  C --> D
  D --> E[dataset.py<br/>Combines WSI + Omics + Labels]
  E --> F[train.py<br/>Model Training]
  E --> G[validate.py<br/>Evaluation]
  F --> H[predict.py<br/>Predictions, CSV, ROC]
  G --> H
```

---

## 🧠 Model Architecture Overview

```
flowchart TD
  subgraph Inputs
    A1[WSI Patch Input<br/>(B, N, 1024)]
    A2[Omics Input<br/>RNA-seq, Methylation, miRNA, RPPA]
  end

  A1 --> B1[WSI Transformer Encoder (ViT)]
  A2 --> B2[Omics Sub-Encoders ×4<br/>Linear + Transformer]

  B1 --> C1[WSI Patch Tokens<br/>(B, N, D)]
  B2 --> C2[Omics Tokens]

  C1 --> D[Cross-Modal Transformer Encoder]
  C2 --> D

  D --> E[Integrated Vector<br/>(B, D)]

  E --> F1[TIL Head<br/>Multi-label Classification]
  E --> F2[Response Head<br/>Binary Classification]
  E --> F3[Survival Head<br/>Regression]
```

---

## ✅ Supported Tasks

- **TIL Classification** – Multi-label classification  
- **Response Prediction** – Binary classification  
- **Survival Prediction** – Regression

---

## 🔁 Reproducibility Steps

1. **Prepare Raw Data**  
   - Place `.pt` files into `Immunova2_Module1/`  
   - Place omics & clinical CSVs into `Immunova2_module2/`

2. **Extract Features**
   ```bash
   python WSI_feature.py
   python Omics_feature.py
   python label_feature.py
   ```

3. **Train Model**
   ```bash
   python train.py
   ```

4. **Validate Model**
   ```bash
   python validate.py
   ```

5. **Generate Predictions**
   ```bash
   python predict.py
   ```

---

## 📦 Optional: Environment Setup

You can use `conda` to manage dependencies:

```bash
conda create -n immunova python=3.9
conda activate immunova
pip install -r requirements.txt
```

---

## 📄 Citation

If you use this repository in your work, please consider citing the associated paper or repository.

> 🔬 Add citation information here (BibTeX, DOI, or preprint link)

---

## 📬 Contact

For questions or collaborations, please contact:  
**Your Name** — [your.email@example.com](mailto:your.email@example.com)

---
