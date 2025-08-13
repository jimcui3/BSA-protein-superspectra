# BSA-protein-superspectra

## Protein Spectra Dataset

This repository contains experimental BSA superspectra datasets generated from surface-enhanced Raman spectroscopy (SERS) measurements.  
The data include raw and processed spectra collected on SiO₂ substrates with four different surface modifications (`Bare`, `Cysteamine`, `Cysteine`, and `MCH`).  
Superspectra (`S1–S4`) are formed by concatenating spectral blocks (401–1799 cm⁻¹) from one or more modifications, in the order specified by the filenames.  
The repository also provides the master dataset, pre-split training/testing subsets, and a Jupyter notebook for generating superspectra and training machine learning models (SVR and RF).

---

## 📂 Repository structure
```
.
├── 07172025 - Super spectra BSA (final).ipynb      # End-to-end notebook: load CSV, make splits, build superspectra, train SVR/RF
├── README.md
└── data/
    ├── 07052025 - BSA-thiol_dataset_corrected.csv   # Preprocessed spectra for all four surface modifications (Bare, Cysteamine, Cysteine, MCH)
    │
    ├── S1/                                          # Spectra built from a single modification (trivial concat)
    │   └── train_test_set{1..10}/                   # 10 random splits
    │       ├── train_dataset{N}.csv
    │       ├── test_dataset{N}.csv
    │       ├── train_split/
    │       │   ├── Bare-BSA.csv
    │       │   ├── Cysteamine-BSA.csv
    │       │   ├── Cysteine-BSA.csv
    │       │   └── MCH-BSA.csv
    │       └── test_split/
    │           ├── Bare-BSA.csv
    │           ├── Cysteamine-BSA.csv
    │           ├── Cysteine-BSA.csv
    │           └── MCH-BSA.csv
    │
    ├── S2/                                          # S2 superspectra (pairwise concatenation across modifications)
    │   └── 2x2_{1..10}/
    │       ├── Bare_Cysteamine_train{N}.csv     ┆ Bare_Cysteamine_test{N}.csv
    │       ├── Bare_Cysteine_train{N}.csv       ┆ Bare_Cysteine_test{N}.csv
    │       ├── Bare_MCH_train{N}.csv            ┆ Bare_MCH_test{N}.csv
    │       ├── Cysteamine_Cysteine_train{N}.csv ┆ Cysteamine_Cysteine_test{N}.csv
    │       ├── Cysteamine_MCH_train{N}.csv      ┆ Cysteamine_MCH_test{N}.csv
    │       └── Cysteine_MCH_train{N}.csv        ┆ Cysteine_MCH_test{N}.csv
    │
    ├── S3/                                          # S3 superspectra (triplet concatenation)
    │   └── 3x3_{1..10}/
    │       ├── Bare_Cysteamine_Cysteine_train{N}.csv  ┆ Bare_Cysteamine_Cysteine_test{N}.csv
    │       ├── Bare_Cysteamine_MCH_train{N}.csv       ┆ Bare_Cysteamine_MCH_test{N}.csv
    │       ├── Bare_Cysteine_MCH_train{N}.csv         ┆ Bare_Cysteine_MCH_test{N}.csv
    │       └── Cysteamine_Cysteine_MCH_train{N}.csv   ┆ Cysteamine_Cysteine_MCH_test{N}.csv
    │
    └── S4/                                          # S4 superspectra (concatenate all four modifications)
        └── 4x4_{1..10}/
            ├── Bare_Cysteamine_Cysteine_MCH_train{N}.csv
            └── Bare_Cysteamine_Cysteine_MCH_test{N}.csv

```

## 📝 File descriptions

### Jupyter notebook
- **`07172025 - Super spectra BSA (final).ipynb`**
  - End-to-end pipeline:
    1. Read `data/07052025 - BSA-thiol_dataset_corrected.csv`.
    2. Produce 10 random splits.
    3. Build superspectra `S1–S4` (concatenate unprefixed `[401–1799]` blocks in filename order).
    4. Train and evaluate SVR & RF.

---

### CSV files

#### 1) Master dataset (all raw spectra)
- **`data/07052025 - BSA-thiol_dataset_corrected.csv`**
- **Rows:** individual BSA spectra (one surface modification per row).
- **Columns:**

| Column               | Description                                                                           |
|----------------------|---------------------------------------------------------------------------------------|
| `label`              | Surface modification: `Bare`, `Cysteamine`, `Cysteine`, or `MCH`                      |
| `conc`               | BSA concentration (units per your experiment/report)                                  |
| `401, 402, …, 1799`  | SERS intensity at Raman shift (cm⁻¹)                                                  |

---

#### 2) `S1/` (single-mod superspectra)
- **Where:** `data/S1/train_test_set{1..10}/`
  - `train_split/` and `test_split/`: per-mod CSVs
  - `train_dataset{N}.csv` and `test_dataset{N}.csv`: model-ready tables

- **Columns (all S1 CSVs):**

| Column               | Description                                                                           |
|----------------------|---------------------------------------------------------------------------------------|
| `label`              | The single modification in that file (`Bare`, `Cysteamine`, `Cysteine`, `MCH`)        |
| `conc`               | BSA concentration                                                                     |
| `401, 402, …, 1799`  | SERS intensity at Raman shift (cm⁻¹)                                                  |

---

#### 3) `S2/`, `S3/`, `S4/` (multi-mod superspectra; concatenated blocks)
- **Where:**
  - `data/S2/2x2_{1..10}/*.csv` (pairs)
  - `data/S3/3x3_{1..10}/*.csv` (triplets)
  - `data/S4/4x4_{1..10}/*.csv` (all four)

- **Filename → column ordering rule:**  
  A file named `Bare_Cysteine_train1.csv` has feature columns formed by **concatenating unprefixed** spectral blocks in this order:
  
  [401..1799] (for Bare), then [401..1799] (for Cysteine)

  
- **Columns (all S2–S4 CSVs):**

| Column                 | Description                                                                                 |
|------------------------|---------------------------------------------------------------------------------------------|
| `label`                | Non-informative here; simply copies the **last** modification listed in the filename        |
| `conc`                 | BSA concentration                                                                           |
| Repeated spectral blocks | Unprefixed `401, 402, …, 1799` for each listed modification, concatenated in filename order |

> Because spectral headers repeat across blocks, select features **by position** (block slicing) rather than by header name. The notebook demonstrates this handling.

---

<!-- ## 🚀 Citation -->
<!-- If you use this dataset in your work, please cite: -->

<!--  J. Cui et al., “MultiplexCR: Deep learning for multi‐virus SERS quantification,” Journal of Raman Spectroscopy, 2025. DOI: 10.xxx/yyy -->

## ⚖️ License
This dataset is released under the MIT license.

## Contact

For any questions, issues, or collaboration opportunities, please reach out via:

📧 **Email**:  
- [jiaheng.cui@uga.edu](mailto:jiaheng.cui@uga.edu)  
- [zhao-nano-lab@uga.edu](mailto:zhao-nano-lab@uga.edu)  

💬 **GitHub**:  
- Open an **Issue** or start a discussion in **GitHub Discussions**.

We welcome feedback and potential collaborations!
