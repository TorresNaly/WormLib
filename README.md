# WormLib <img src="docs/WormLib_logo.png" alt="WormLib Logo" width="350" align="right" />


[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/) [![BigFISH](https://img.shields.io/badge/smFISH-BigFISH-orange.svg)](https://github.com/fish-quant/big-fish) [![Cellpose](https://img.shields.io/badge/segmentation-Cellpose-green.svg)](https://github.com/MouseLand/cellpose)

**WormLib is a modular open-source image analysis library for quantifying microscopy images of *Caenorhabditis elegans* embryos. It provides an end-to-end pipeline from image loading, embryo and cell segmentation, cell identity prediction, spot detection, and spatial mRNA analysis.**
---
You can learn more about Wormlib by reading the paper [here](https://example.com/paper) and in the detailed documentation at [READTHEDOCS.io](https://wormlib.readthedocs.io). Please see install instructions below.

---

## Example notebooks:
[1 - Single-cell spot detection](https://github.com/TorresNaly/WormLib/blob/main/examples/1%20-%20Single-cell%20spot%20detection.ipynb)

---

## Citation

If you use WormLib in your research, please cite:
> **Naly Torres, Luis de Lira Aguilera, Karissa Coleman, Richard Bruno, Brian Munsky, Erin Osborne Nishimura** *WormLib: A Modular Image Analysis Library for Quantifying C. elegans Microscopy.* (In preparation)


---

## Installation

### Quick Install With Conda

```bash
# Clone the repository
git clone https://github.com/erinosb/WormLib.git
cd WormLib

# Create and activate the tested WormLib environment
conda env create -f installation/wormlib.yml
conda activate wormlib
```

This installs the core scientific stack through conda and the remaining
WormLib dependencies through pip. The environment file pins NumPy to `1.26.4`
so Cellpose, PyTorch, and BigFISH do not accidentally run against NumPy 2.x.

If you already have an older or broken `wormlib` environment, remove it first:

```bash
conda deactivate
conda env remove -n wormlib
conda env create -f installation/wormlib.yml
conda activate wormlib
```

### NVIDIA GPU Install

For GPU-accelerated Cellpose segmentation on Linux/Windows with an NVIDIA GPU
and CUDA 12.4-compatible drivers:

```bash
conda env create -f installation/wormlib_cuda.yml
conda activate wormlib
```

On Apple Silicon macOS, use the default `installation/wormlib.yml`. PyTorch MPS
support is included in the regular macOS PyTorch wheels when available.

### Verify Installation

```bash
python -c "import numpy as np; import torch; from cellpose import models; import bigfish; print('NumPy', np.__version__); print('Torch', torch.__version__); print('WormLib dependencies OK')"
```

Expected NumPy version:

```text
NumPy 1.26.4
```

If you see an error like `A module that was compiled using NumPy 1.x cannot be
run in NumPy 2.x` or `_ARRAY_API not found`, the active environment is not using
the pinned WormLib environment. Recreate it from the YAML file above.

---


## Dependencies

| Package | Purpose |
|---------|---------|
| [BigFISH](https://github.com/fish-quant/big-fish) | smFISH spot detection & analysis |
| [Cellpose](https://github.com/MouseLand/cellpose) | Deep learning cell segmentation |
| [scikit-image](https://scikit-image.org/) | Image processing & morphology |
| [scikit-learn](https://scikit-learn.org/) | Random Forest classifiers (transitive via joblib) |
| [PyTorch](https://pytorch.org/) | GPU backend for Cellpose |
| [OpenCV](https://opencv.org/) | Contour & ellipse fitting |
| [nd2](https://github.com/tlambert03/nd2) | Nikon ND2 file reader |
| [tifffile](https://github.com/cgohlke/tifffile) | TIFF file I/O |
| [PyYAML](https://pyyaml.org/) | YAML configuration parsing |
| [ReportLab](https://www.reportlab.com/) | PDF report generation |
| [Pillow](https://python-pillow.org/) | Image handling for PDF reports |

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.


---

## Support

- **Issues & Contributions**: [GitHub](https://github.com/erinosb/WormLib/issues)
- **Repository**: [github.com/erinosb/WormLib](https://github.com/erinosb/WormLib)
