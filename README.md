# EE 559 — Data-Driven Dynamics Learning for MPC
**Steven Fang & Cesar Pineda · USC EE 559 · Spring 2026**

---

## Overview

This repository contains the Jupyter/Colab notebook for our EE 559 course project on nonlinear system identification of the Coupled Electric Drives benchmark using Ridge Regression, Lasso, and a Multi-Layer Perceptron (MLP).

---

## Requirements

- Google Account (for Google Colab and Google Drive)
- No local Python installation required — everything runs in Colab

---

## Dataset Setup

The dataset is sourced from the [Nonlinear Benchmark repository](https://www.nonlinearbenchmark.org/) (Coupled Electric Drives).

1. Download the dataset zip file from the Nonlinear Benchmark site
2. Upload the zip file to your **Google Drive** (anywhere is fine, but the root of My Drive is simplest)
3. Note the filename — it should be something like FULLTEXT01.zip

You can also use the .zip file included in this repository

---

## Running the Notebook

### Step 1 — Open in Colab

Open `EE559_Project.ipynb` directly in Google Colab. If opening from GitHub, click the **Open in Colab** badge or go to:

```
File → Open Notebook → GitHub
```

and paste the repository URL.

### Step 2 — Mount Google Drive

The first code cell mounts your Google Drive so the notebook can access the dataset. Run it and follow the authentication prompt:

```python
from google.colab import drive
drive.mount('/content/drive')
```

Click the link that appears, sign in with your Google Account, copy the authorization code, and paste it into the box. You should see `Mounted at /content/drive` when successful.

### Step 3 — Extract the Dataset

The next cell locates and extracts the zip file from your Drive into the Colab working directory. Update the path if you placed the zip in a subfolder:

```python
import zipfile, os

zip_path = '/content/drive/MyDrive/CoupledElectricDrives.zip'  # update if needed
extract_path = '/content/'

with zipfile.ZipFile(zip_path, 'r') as z:
    z.extractall(extract_path)

print("Extracted files:")
print(os.listdir(extract_path))
```

After extraction you should see `DATAPRBS.csv` and `DATAUNIF.csv` listed. If they are inside a subfolder after extraction, update `DATA_DIR` in the **Imports & Configuration** cell to point to that subfolder:

```python
DATA_DIR = "/content/CoupledElectricDrives/"  # adjust to match extracted folder name
```

### Step 4 — Run All Cells

Once the data is extracted and `DATA_DIR` is set correctly, run all cells in order:

```
Runtime → Run All
```

Or run section by section using `Shift + Enter`. The notebook is organized into the following sections:

| Section | Contents |
|---|---|
| 1 | Imports & configuration |
| 2 | Data loading & trajectory plots |
| 3 | NARX feature construction & splits |
| 4 | Ridge Regression (baseline) |
| 5 | Lasso Regression (baseline) |
| 6 | MLP Neural Network (baseline) |
| 7 | Nonlinear Library Lasso (extended) |
| 8 | MLP with ReLU output (extended) |
| 9 | Model comparison & multi-step simulation |

---

## Expected Runtime

| Section | Approximate time |
|---|---|
| Sections 1–3 (data loading) | < 1 min |
| Section 4 (Ridge) | < 1 min |
| Section 5 (Lasso) | 1–2 min |
| Section 6 (MLP) | 2–4 min |
| Section 7 (Nonlinear Lasso) | 3–5 min |
| Section 8 (ReLU MLP) | 2–4 min |
| Section 9 (simulation & plots) | 1–2 min |

Total end-to-end runtime is approximately **10–15 minutes** on a standard Colab CPU instance. No GPU is required.

---

## Output Files

The following plots are saved to `/content/` after running:

- `trajectories.png` — all five input/output trajectories
- `ridge_results.png` — Ridge lag search, regularization path, test prediction
- `lasso_results.png` — Lasso regularization path, coefficients, test prediction
- `mlp_results.png` — MLP training curve and test prediction
- `lasso_library_results.png` — Nonlinear Lasso coefficients and test prediction
- `relu_mlp_results.png` — ReLU MLP training curve and test prediction
- `simulation_all_models.png` — Multi-step simulation comparison (2×3 grid)

To save these to your Drive, add the following after the final cell:

```python
import shutil
shutil.copy('/content/simulation_all_models.png',
            '/content/drive/MyDrive/simulation_all_models.png')
```

---

## Troubleshooting

**`FileNotFoundError: DATAPRBS.csv`**
The `DATA_DIR` variable does not point to where the CSV files were extracted. Run `os.listdir('/content/')` to see what's there and update `DATA_DIR` accordingly.

**Drive mount fails or times out**
Re-run the mount cell and complete the authentication again. Colab sessions occasionally drop the Drive connection after long idle periods.

**Lasso takes longer than expected**
The nonlinear library Lasso (Section 7) operates on 70 features and can take up to 5 minutes. This is expected — the vectorized coordinate descent is already applied. Do not interrupt this cell; it will complete.

**`KeyError` on trajectory columns**
Confirm the extracted CSV filenames are exactly `DATAPRBS.csv` and `DATAUNIF.csv` (case-sensitive). Some zip extractors may alter capitalization on certain operating systems.
