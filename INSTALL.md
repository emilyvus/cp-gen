# CP-GEN — Installation & Setup Guide

---

## Prerequisites

- [Conda](https://docs.conda.io/en/latest/miniconda.html) (Miniconda or Anaconda) installed on your system
- Git (to clone the repository)

---

## Step 1 — Create a Conda Environment

### On **Linux** or **macOS**

Open a terminal and run:

```bash
conda create -n cp-gen python=3.12 -y
```

> This creates a new isolated environment named **`cp-gen`** with Python 3.12.

Activate the environment:

```bash
conda activate cp-gen
```

You should see `(cp-gen)` at the beginning of your terminal prompt.

---

##  Step 2 — Install Dependencies from `pyproject.toml`

Make sure you are in the root directory of the project (where `pyproject.toml` is located), then run:

```bash
pip install -e .
```

> The `-e` flag installs the project in **editable mode**, so any local changes are immediately reflected without reinstalling. This repository is a notebook collection rather than an importable package, so this step only installs the dependencies below.

This will install all required packages including:

| Package | Purpose |
|---|---|
| `numpy` | Numerical computing |
| `pandas` | Data manipulation |
| `scipy` | Scientific computing & statistics |
| `scikit-learn` | Machine learning (clustering, regression, SVM) |
| `matplotlib` | Plotting & visualization |
| `seaborn` | Statistical data visualization |
| `jupyterlab` | JupyterLab IDE |
| `notebook` | Classic Jupyter Notebook |
| `ipykernel` | Jupyter kernel for Python |

---

## Step 3 — Register the Conda Environment as a Jupyter Kernel

```bash
python -m ipykernel install --user --name cp-gen --display-name "Python (cp-gen)"
```

>  This makes the `cp-gen` environment available as a kernel inside JupyterLab and Jupyter Notebook.

---

## Step 4 — Launch Jupyter and Run the Notebooks

### Option A — JupyterLab (recommended)

```bash
jupyter lab
```

### Option B — Classic Jupyter Notebook

```bash
jupyter notebook
```

Both commands will open a browser window. Navigate to the **`notebooks/`** folder and open any `.ipynb` file.

> If the browser does not open automatically, copy the URL printed in the terminal (e.g., `http://localhost:8888/lab?token=...`) and paste it into your browser.

---

## Available Notebooks

Notebooks are organized by case study under `notebooks/`. See the notebook index in [README.md](README.md) for a description of each one.

| Case study | Folder | Notebooks |
|---|---|---|
| ALS → FTD | `notebooks/als-ftd/` | Clustering (Silhouette Coefficient), t-SNE, PCA, statistical heatmap, in-disease estimation, cross-disease estimation, CP-GEN vs GroupMean |
| CD → AS | `notebooks/cd-as/` | Clustering (Silhouette Coefficient), t-SNE, PCA, statistical heatmap, in-disease estimation, cross-disease estimation, CP-GEN vs GroupMean |

Each case-study folder also contains a `mylib.py` with helper functions (genome ID lists, variant counting, statistics) and a `*_population2cluster_AggCluster_k5.csv` with the k=5 population-to-cluster assignments. The `mylib.py` files are per-folder copies, so import them from within their own directory.

---

## Deactivating the Environment

When you are done, deactivate the conda environment:

```bash
conda deactivate
```

---

##  Removing the Environment (Optional)

To completely remove the `cp-gen` environment:

```bash
conda remove -n cp-gen --all -y
```

---

##  Troubleshooting

- **`conda: command not found`** — Make sure Conda is installed and your shell is initialized: `conda init bash` (or `zsh`), then restart your terminal.
- **Kernel not showing in Jupyter** — Re-run the `ipykernel install` command from Step 3.
- **Package conflicts** — Try upgrading pip first: `pip install --upgrade pip`, then re-run `pip install -e .`
- **`ModuleNotFoundError: No module named 'mylib'`** — Launch Jupyter from the repository root and open the notebook from inside its own case-study folder; each folder has its own `mylib.py`.
