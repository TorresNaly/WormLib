# WormLib <img src="docs/WormLib_logo.png" alt="WormLib Logo" width="150" align="right" />

**Authors:** Erin Osborne Nishimura and contributors

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/) [![BigFISH](https://img.shields.io/badge/smFISH-BigFISH-orange.svg)](https://github.com/fish-quant/big-fish) [![Cellpose](https://img.shields.io/badge/segmentation-Cellpose-green.svg)](https://github.com/MouseLand/cellpose)

## About

**WormLib** is a modular open-source image analysis library for quantifying *Caenorhabditis elegans* microscopy images. It provides an end-to-end pipeline from image loading through embryo segmentation, cell identity prediction, single-molecule FISH (smFISH) spot detection, and spatial mRNA analysis.

<p align="center">
  <img src="docs/WormLib_logo.png" alt="WormLib" width="300" />
</p>

---

## Features

- **Image I/O**: Load DeltaVision (.dv), Nikon (.nd2), and TIFF images with multi-channel extraction
- **Embryo segmentation**: Cellpose-based whole-embryo detection with size-outlier filtering
- **Cell segmentation**: Automated nucleus-cytosol pairing with diameter optimization (≤4-cell embryos)
- **Cell classification**: Random Forest classifiers for blastomere identity prediction
  - **2-cell stage**: AB vs P1 with proximity fail-safe
  - **4-cell stage**: ABa, ABp, EMS, P2 with ellipse-based positional assignment
- **smFISH spot detection**: BigFISH pipeline with LoG filtering, automated thresholding, and dense region decomposition
- **Cluster detection**: Identify transcription sites and mRNA clusters
- **Per-cell quantification**: Spot counting per segmented cell with classifier labels
- **Spatial mRNA analysis**:
  - Grid-based mRNA abundance heatmaps
  - RNA density profiles along the anterior-posterior (AP) axis
  - Line scan intensity analysis with ROI restriction
- **PDF report generation**: Automated reports with figures, tables, and analysis logs
- **HPC batch processing**: SLURM array job support for high-throughput analysis

---

## Installation

### Quick Install

```bash
# Create and activate conda environment
conda create -n wormlib python=3.10 -y
conda activate wormlib

# Clone the repository
git clone https://github.com/erinosb/WormLib.git
cd WormLib

# Install dependencies
pip install -r requirements.txt
```

### GPU Support (Recommended)

For GPU-accelerated Cellpose segmentation:

```bash
# NVIDIA GPU (Linux/Windows)
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu124

# Apple Silicon (macOS) — MPS support is automatic with:
pip install torch torchvision
```

### Verify Installation

```python
import bigfish
import cellpose
from scipy.ndimage import label
from skimage import measure
print("WormLib dependencies OK")
```

---

## Usage

### 1. Run the Bundled Example

```bash
python examples/wormlib_example.py
```

The example analyzes the sample DeltaVision image in `data/08_dv/` and writes
figures, CSV files, and `report.pdf` to `output_temp/`. This is the quickest
way to confirm that your Python environment, Cellpose, BigFISH, and model files
are working on your computer.

### 2. Prepare Your Own Images

For the full pipeline, WormLib expects microscopy data with:

- one or two smFISH RNA channels
- a nuclear channel, typically DAPI
- a brightfield/reference image for embryo and cell segmentation

Supported input formats are `.dv`, `.nd2`, `.tif`, and `.tiff`. The command-line
pipeline works best when you pass a folder containing one embryo/acquisition.

#### DeltaVision `.dv`

Use one folder per acquisition. A typical DeltaVision folder looks like:

```text
my_experiment/
└── input/
    └── embryo_001/
        ├── embryo_001_R3D.dv       # 4D stack: channel, z, y, x
        └── embryo_001_R3D_REF.dv   # 2D brightfield/reference image
```

For `.dv` stacks, WormLib assigns channels by order:

| Channel index | WormLib name | Typical use |
|---------------|--------------|-------------|
| 0 | `Cy5` | RNA channel 1 |
| 1 | `mCherry` | RNA channel 2 |
| 2 | `FITC` | optional channel |
| 3 | `DAPI` | nuclei |

#### Nikon `.nd2`

For `.nd2`, WormLib expects a stack shaped like time/z by channel by y by x.
Channels are assigned in this order:

| Channel index | WormLib name | Typical use |
|---------------|--------------|-------------|
| 0 | `Cy5` | RNA channel 1 |
| 1 | `mCherry` | RNA channel 2 |
| 2 | `FITC` | optional channel |
| 3 | `DAPI` | nuclei |
| 4 | `brightfield` | brightfield/reference |

TIFF loading is available, but the full segmentation + smFISH workflow is most
straightforward with DV or ND2 data that includes both nuclear and brightfield
information.

### 3. Run Your Own Image Locally

From the repository root, set the input folder, output folder, microscope
metadata, channel names, and pipeline options:

```bash
conda activate wormlib
cd /path/to/WormLib

mkdir -p /path/to/results/embryo_001

export FOLDER_NAME="/path/to/my_experiment/input/embryo_001"
export OUTPUT_DIRECTORY="/path/to/results/embryo_001"

# Microscope calibration, in nm, as Z,Y,X
export VOXEL_SIZE="1448,450,450"
export SPOT_RADIUS_CH0="1409,340,340"
export SPOT_RADIUS_CH1="1283,310,310"

# Output labels for channels. Use FITC="nothing" if no FITC channel exists.
export Cy5="mRNA1"
export mCherry="mRNA2"
export FITC="nothing"
export DAPI="DAPI"
export brightfield="brightfield"

# Pipeline switches
export RUN_CELL_SEGMENTATION="True"
export RUN_CELL_CLASSIFIER="True"
export RUN_EMBRYO_SEGMENTATION="False"
export SPOT_DETECTION="True"
export RUN_mRNA_HEATMAPS="True"
export RUN_RNA_DENSITY_ANALYSIS="True"
export RUN_LINE_SCAN_ANALYSIS="True"

python src/wormlib.py
```

You can also pass the input and output paths directly:

```bash
python src/wormlib.py /path/to/my_experiment/input/embryo_001 /path/to/results/embryo_001
```

WormLib automatically detects `.dv`, `.nd2`, `.tif`, or `.tiff` files in the
input path.

### 4. Configure the Example Script Instead

If you prefer editing a Python file, open `examples/wormlib_example.py` and
change Section 1:

```python
# Define image path and microscope parameters
image_path = Path("/path/to/embryo_001_R3D.dv")
image_ref = Path("/path/to/embryo_001_R3D_REF.dv")
output_directory = Path("/path/to/results/embryo_001")

voxel_size = (1448, 450, 450)        # Z, Y, X in nm
spot_radius_ch0 = (1409, 340, 340)   # PSF for Cy5 channel
spot_radius_ch1 = (1283, 310, 310)   # PSF for mCherry channel

# Channel assignments
channel_names = {
    "Cy5": "mRNA1",
    "mCherry": "mRNA2",
    "FITC": None,
    "DAPI": "DAPI",
    "brightfield": "brightfield",
}

# Enable pipeline steps
run_cell_segmentation = True
run_cell_classifier = True
run_spot_detection = True
run_mRNA_heatmaps = True
run_rna_density_analysis = True
run_line_scan_analysis = True
```

Then run:

```bash
python examples/wormlib_example.py
```

### 5. Understand the Outputs

Each output directory can include:

| Output | Description |
|--------|-------------|
| `channels_*.png` | Loaded channel overview |
| `cell_segmentation_*.png` or `embryo_segmentation_*.png` | Segmentation result |
| `features_df_*.csv` | Cell morphology and classifier features |
| `total_mRNA_counts_*.csv` | Total molecule counts per RNA channel |
| `per_cell_mRNA_counts_*.csv` | Per-cell counts and predicted labels, when classification succeeds |
| `quantification_cell_*.csv` | Compatibility copy of per-cell counts for batch aggregation |
| `*_detection_*.png`, `*_threshold_*.png` | BigFISH detection diagnostics |
| `*_AP_profile_data_*.csv` | RNA density along the AP axis |
| `*_line_scan_data_*.csv` and `*_line_density_data_*.csv` | ROI line-scan outputs |
| `report.pdf` | Summary report with figures and tables |

If cell segmentation or blastomere classification is not reliable for an image,
WormLib falls back to whole-embryo segmentation and skips per-cell labels.

### HPC Batch Processing (SLURM)

```bash
# Submit one array task per input subfolder.
# Example: if input/ has 20 embryo folders, use --array=0-19.
sbatch --array=0-N examples/run-WormLib.sh /path/to/my_experiment

# The script automatically:
# 1. Processes each image subdirectory
# 2. Saves timestamped script snapshots
# 3. Combines per-image CSVs into aggregate outputs
```

Before submitting, edit `examples/run-WormLib.sh` to match your microscope
calibration, channel names, and desired pipeline switches.

---

## Analysis Pipeline

```text
┌──────────────┐    ┌──────────────────┐    ┌─────────────────┐
│  Image I/O   │───▶│  Segmentation    │───▶│  Classification │
│  DV/ND2/TIFF │    │  Embryo + Cells  │    │  AB/P1 or 4-cell│
└──────────────┘    └──────────────────┘    └────────┬────────┘
                                                     │
                    ┌──────────────────┐              │
                    │  Spot Detection  │◀─────────────┘
                    │  smFISH (BigFISH)│
                    └────────┬─────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
     ┌─────────────┐ ┌────────────┐ ┌────────────┐
     │  Heatmaps   │ │  Density   │ │  Line Scan │
     │  mRNA grid  │ │  AP axis   │ │  ROI-based │
     └──────┬──────┘ └─────┬──────┘ └─────┬──────┘
            │              │              │
            └──────────────┼──────────────┘
                           ▼
                   ┌──────────────┐
                   │  PDF Report  │
                   │  + CSV Export│
                   └──────────────┘
```

---

## Project Structure

```text
WormLib/
├── src/
│   └── wormlib.py                # Main analysis engine
├── examples/
│   ├── wormlib_example.py        # Pipeline example script
│   └── run-WormLib.sh            # SLURM batch script
├── models/                       # Trained ML classifiers
│   ├── 2-cell_classification_RFmodel.joblib
│   ├── 4-cell_classification_RFmodel.joblib
│   └── ce-embryo                 # Cellpose pretrained embryo model
├── data/                         # Sample microscopy images
│   ├── 04_dv/                    # DeltaVision samples
│   ├── 05_dv/
│   ├── 08_dv/
│   └── 1886_nd2/                 # Nikon ND2 samples
├── docs/                         # Documentation and assets
│   └── WormLib_logo.png
├── .gitignore
├── requirements.txt              # Python dependencies
└── LICENSE                       # MIT License
```

---

## Dependencies

| Package | Purpose |
|---------|---------|
| [BigFISH](https://github.com/fish-quant/big-fish) | smFISH spot detection & analysis |
| [Cellpose](https://github.com/MouseLand/cellpose) | Deep learning cell segmentation |
| [scikit-image](https://scikit-image.org/) | Image processing & morphology |
| [scikit-learn](https://scikit-learn.org/) | Random Forest classifiers |
| [PyTorch](https://pytorch.org/) | GPU backend for Cellpose |
| [OpenCV](https://opencv.org/) | Contour & ellipse fitting |
| [nd2](https://github.com/tlambert03/nd2) | Nikon ND2 file reader |
| [tifffile](https://github.com/cgohlke/tifffile) | TIFF file I/O |
| [ReportLab](https://www.reportlab.com/) | PDF report generation |

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

## Citation

If you use WormLib in your research, please cite:

> **Nishimura EO et al.** *WormLib: A Modular Image Analysis Library for Quantifying C. elegans Microscopy.* (In preparation)

---

## Support

- **Issues & Contributions**: [GitHub](https://github.com/erinosb/WormLib/issues)
- **Repository**: [github.com/erinosb/WormLib](https://github.com/erinosb/WormLib)
