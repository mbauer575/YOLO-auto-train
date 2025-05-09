# YOLO Auto Train

This project automates the process of augmenting, annotating, and training a YOLO model using Ultralytics and autodistill. The repository provides scripts for data augmentation, labeling images with GroundingDINO, training a YOLO model, parameter sweeps, and easy pipeline execution on HPC clusters.

## Overview

- **Augmentation Script (`src/augment.py`)
  Applies brightness and blur augmentations to raw images.
  *Usage:*
  ```powershell
  python src/augment.py [input_folder] [output_folder] [brightness_up] [brightness_down] [blur_kernel]
  ```

- **Labeling Script (`src/label.py`)
  Uses GroundingDINO to annotate augmented images.
  *Usage:*
  ```powershell
  python src/label.py [input_folder] [output_folder]
  ```

- **Training Script (`src/train.py`)**
  Trains a YOLO model on the labeled dataset and exports the model.
  *Usage:*
  ```powershell
  python src/train.py [labeled_folder] [model_type] [export_format] [image_size]
  ```

- **Pipeline Driver (`run_pipeline.sh`)]**
  Chains augmentation, labeling, and training steps in one script. Expects environment variables:
  `MODEL_TYPE`, `IMAGE_SIZE`, `BRIGHT_POS`, `BRIGHT_NEG`, `BLUR`.
  *Usage:*
  ```powershell
  bash run_pipeline.sh
  ```

- **SLURM Submit Scripts**
  - `submit.bash`: Single-job SLURM script for a fixed set of parameters.
  - `submit-multi.bash`: SLURM array script to sweep over parameter combinations in `combos.csv`.

## License

This project is licensed under the GNU General Public License v3.0.  
See [LICENSE](./LICENSE) for the full terms.

## Requirements

Three separate Python virtual environments are used:

#### Augmentation Environment
- Python 3.10
- `opencv-python`
- `numpy`

Install:
```powershell
pip install -r req/augment-requirements.txt
```

#### Labeling Environment
- Python 3.10
- `torch`
- `transformers`
- `GroundingDINO`
- `numpy`

Install:
```powershell
pip install -r req/label-requirements.txt
```

#### Training Environment
- Python 3.8
- `ultralytics`
- `torch`
- `onnx`
- `numpy`

Install:
```powershell
pip install -r req/train-requirements.txt
```

Ensure required Python versions and GPU support are available.

## Project Structure

```
.
├── combos.csv
├── logs/                      # SLURM job output logs
├── req/
│   ├── augment-requirements.txt
│   ├── label-requirements.txt
│   └── train-requirements.txt
├── run_pipeline.sh            # Pipeline driver script
├── submit.bash                # Single-job SLURM script
├── submit-multi.bash          # Array-job SLURM sweep script
└── src/
    ├── augment.py
    ├── label.py
    └── train.py
```

## Usage

1. **Clone** the repository.
2. **Prepare** a folder of raw images.
3. **Augmented, labeled, and trained** in one shot:
   ```powershell
   $env:MODEL_TYPE="yolov8n.pt"
   $env:IMAGE_SIZE=640
   $env:BRIGHT_POS=0.2
   $env:BRIGHT_NEG=0.1
   $env:BLUR=9
   bash run_pipeline.sh
   ```
4. **SLURM Single Job**:
   - Edit parameters in `submit.bash` and run:
     ```powershell
     sbatch submit.bash
     ```
5. **SLURM Parameter Sweep**:
   - Populate `combos.csv` with comma-separated:
     `model_type,image_size,bright_pos,bright_neg,blur`
   - Run array:
     ```powershell
     sbatch submit-multi.bash
     ```

## Logs

SLURM outputs in `logs/`, named by `%x_%a.out` and `%x_%a.err`.

## Third‑party libraries & licenses

• ultralytics (YOLOv8) — AGPL 3.0  
  https://github.com/ultralytics/ultralytics/blob/main/LICENSE
• GroundingDINO — Apache 2.0  
  https://github.com/IDEA-Research/GroundingDINO  
• OpenCV — Apache 2.0  
  https://opencv.org/  
• PyTorch — BSD‑style  
  https://pytorch.org/  
• numpy — BSD  
  https://numpy.org/  
• transformers — Apache 2.0  
  https://github.com/huggingface/transformers  
• onnx — MIT  
  https://github.com/onnx/onnx  

## Contact

For questions, contact Maxwell Bauer at bauermax@oregonstate.edu.
