# FYP — Ensemble Anomaly Detection

Project notebooks and artifacts for an ensemble-based anomaly detection pipeline.

## Overview

This workspace contains Jupyter notebooks, dataset CSVs, and exported model/pipeline artifacts used for experiments with ensemble models for detecting attacks vs. benign samples.

## Repository structure

- `ensemble.ipynb`, `ensemble1.ipynb` — main experiment notebooks.
- `datasets/` — CSV datasets used by the notebooks:
  - `attack_samples_10sec.csv`
  - `attack_samples_5sec.csv`
  - `benign_samples_10sec.csv`
  - `benign_samples_5sec.csv`
- `export/` — trained model and pipeline artifacts:
  - `gru_feature_extractor.pth` — PyTorch GRU feature extractor weights
  - `pipeline_artifacts.joblib` — serialized scikit-learn / preprocessing pipeline

## Requirements

Install common Python packages used by the notebooks:

```bash
python -m pip install --user jupyterlab numpy pandas scikit-learn torch joblib matplotlib seaborn
```

If you maintain a `requirements.txt`, prefer installing from it:

```bash
pip install -r requirements.txt
```

## Quick start

1. Open the workspace and start Jupyter Lab or Notebook in the `notebooks` folder:

```bash
jupyter lab
# or
jupyter notebook
```

2. Open `ensemble1.ipynb` (or `ensemble.ipynb`) and run cells top-to-bottom. Ensure the `datasets/` and `export/` folders are present relative to the notebook.

3. If you need to re-create the trained artifacts, locate training code cells in the notebooks and run training cells (may require GPU for `torch` models).

## Notes

- The notebooks assume the datasets are in `datasets/` and will read CSV files by name.
- The `export/` folder contains pre-trained artifacts used by evaluation and inference cells.

## Contact

For questions or changes, open an issue or contact the project owner.

## Datasets (download separately)

Do not commit raw dataset files to this repository. Download the CIC IIoT 2025 dataset from the official CIC site (or the provider's distribution page) and convert the data to CSV files placed into the `datasets/` folder.

Recommended workflow:

1. Download the dataset (often distributed as PCAPs or other raw capture formats). Search for "CIC IIoT 2025 dataset" on the official CIC datasets page or the dataset provider.

2. Convert PCAPs/traffic captures to flow CSVs. Two common options:

- CICFlowMeter (recommended for flow features):

```bash
# Download and run CICFlowMeter (Java required)
java -jar CICFlowMeter.jar /path/to/pcap_folder /path/to/output_flows.csv
```

- tshark (field-level CSV export):

```bash
# Extract common fields from a single pcap into CSV
tshark -r input.pcap -T fields \
  -e frame.time_epoch -e ip.src -e ip.dst \
  -e tcp.srcport -e tcp.dstport -e udp.srcport -e udp.dstport \
  -e frame.len \
  -E header=y -E separator=, > output.csv
```

3. Rename or split resulting CSVs to match the notebook expectations and place them in `datasets/`:

```
mv output.csv datasets/attack_samples_10sec.csv
# or
mv output.csv datasets/benign_samples_5sec.csv
```

4. Verify file names in the notebooks (`ensemble.ipynb`, `ensemble1.ipynb`) and update paths if your CSV filenames differ.

Notes:
- Converting raw captures to the exact feature set used in these notebooks may require running the same flow extractor and feature configuration used in the original experiments (e.g., CICFlowMeter default features). If you need help matching features, I can inspect the notebooks and suggest the extraction settings.
- Keep the original raw files outside the repo or store them in a separate dataset storage (external drive or cloud). Only place the derived CSVs into `datasets/` when needed for experiments.
